CREATE OR REPLACE PROCEDURE PDO_GENERATION AS
    MOTHER_COIL               VARCHAR2(50);
    DAUGHTER_COIL             VARCHAR2(50);
    SUB_COIL                  VARCHAR2(50);
    PART_NUM                  INT := 0;
    ZERO_NUMBER               INT;
    H_STM_ID                  VARCHAR2(100);
    V_CURR_COIL               VARCHAR2(50);
    V_NEXT_COIL               VARCHAR2(50);
    V_ACT_LENGTH              NUMBER := 0;
    V_CGD_TS_START_TIME       DATE;
    V_CGD_TS_END_TIME         DATE := SYSDATE;
    V_CGD_WORK_DUR            NUMBER := 0;
    V_ENTRY_ACCU_TENSION_ACT  NUMBER := 0;
    V_EXIT_ACCU_TEN_ACT       NUMBER := 0;
    V_ENTRY_SPEED_ACT         NUMBER := 0;
    V_POR_TENSION_ACT         NUMBER := 0;
    V_CUSTOMER_NAME           VARCHAR2(100);
    V_INPUT_THICKNESS         NUMBER := 0;
    V_INPUT_WIDTH             NUMBER := 0;
    V_INPUT_WEIGHT            NUMBER := 0;
    V_ACT_WIDTH               NUMBER := 0; -- This should be set elsewhere if used
    V_NET_WEIGHT              NUMBER := 0;
    V_DAUGHTER_COIL_WEIGHT    NUMBER := 0;
    REC_SEC                   NUMBER := 0;  -- Needs assignment logic
    LIMIT_TIME                NUMBER := 360;
    FINAL_COIL_ID             VARCHAR2(50);

BEGIN
    H_STM_ID := 'SELECT MOTHER COIL';
    
    SELECT COIL_ID,
           LENGTH(COIL_ID) - LENGTH(TRIM(TRAILING '0' FROM COIL_ID))
    INTO MOTHER_COIL, ZERO_NUMBER
    FROM T_EVENT_TRACKING
    WHERE ID_APP_TAG_EVENT = 'RECOILER';

    IF MOTHER_COIL IS NOT NULL AND MOTHER_COIL != 'NO_COIL' THEN
        SUB_COIL := SUBSTR(MOTHER_COIL, 1, LENGTH(MOTHER_COIL) - ZERO_NUMBER);

        IF REC_SEC < LIMIT_TIME THEN
            H_STM_ID := 'PART NUM COUNT';
            
            SELECT COUNT(CGD_ID_COIL_MTHR)
            INTO PART_NUM
            FROM T_PDO_INFO
            WHERE CGD_ID_COIL_MTHR = MOTHER_COIL;

            PART_NUM := PART_NUM + 1;

            CASE ZERO_NUMBER
                WHEN 1 THEN DAUGHTER_COIL := SUB_COIL || TO_CHAR(PART_NUM);
                WHEN 2 THEN DAUGHTER_COIL := SUB_COIL || TO_CHAR(PART_NUM * 10);
                WHEN 3 THEN DAUGHTER_COIL := SUB_COIL || TO_CHAR(PART_NUM * 100);
                WHEN 4 THEN DAUGHTER_COIL := SUB_COIL || '0' || TO_CHAR(PART_NUM * 100);
                WHEN 5 THEN DAUGHTER_COIL := SUB_COIL || '00' || TO_CHAR(PART_NUM * 100);
                ELSE DAUGHTER_COIL := NULL;
            END CASE;

            H_STM_ID := 'FETCH LENGTH & START TIME';
            SELECT ROUND(NVL(MAX(ACT_LENGTH), 0), 2)
            INTO V_ACT_LENGTH
            FROM T_PERIODIC_VALUE_LOG
            WHERE DATE_TIME BETWEEN (V_CGD_TS_END_TIME - INTERVAL '5' MINUTE) AND V_CGD_TS_END_TIME;

            SELECT DATE_TIME
            INTO V_CGD_TS_START_TIME
            FROM (
                SELECT DATE_TIME FROM T_EVENT_LOG
                WHERE ID_APP_TAG = 'RECOILER' AND VALUE = 1
                ORDER BY DATE_TIME DESC
            ) WHERE ROWNUM = 1;

            V_CGD_WORK_DUR := (
                EXTRACT(MINUTE FROM (V_CGD_TS_END_TIME - V_CGD_TS_START_TIME)) +
                EXTRACT(HOUR FROM (V_CGD_TS_END_TIME - V_CGD_TS_START_TIME)) * 60 +
                EXTRACT(DAY FROM (V_CGD_TS_END_TIME - V_CGD_TS_START_TIME)) * 24 * 60
            );

            H_STM_ID := 'FETCH SPEED';
            SELECT ROUND(NVL(AVG(ENTRY_SPEED_ACT), 0), 2)
            INTO V_ENTRY_SPEED_ACT
            FROM T_PERIODIC_VALUE_LOG
            WHERE DATE_TIME BETWEEN V_CGD_TS_START_TIME AND V_CGD_TS_END_TIME
              AND ENTRY_SPEED_ACT != 0;

            H_STM_ID := 'FETCH TENSION';
            SELECT ROUND(NVL(AVG(ENTRY_ACCU_TENSION_ACT), 0), 2),
                   ROUND(NVL(AVG(EXIT_ACCU_TEN_ACT), 0), 2),
                   ROUND(NVL(AVG(POR_TENSION_ACT), 0), 2)
            INTO V_ENTRY_ACCU_TENSION_ACT,
                 V_EXIT_ACCU_TEN_ACT,
                 V_POR_TENSION_ACT
            FROM T_PERIODIC_VALUE_LOG
            WHERE DATE_TIME BETWEEN V_CGD_TS_START_TIME AND V_CGD_TS_END_TIME;

            H_STM_ID := 'FETCH PDI';
            SELECT CGP_MK_CUSTOMER,
                   CGP_SEC1_ORD,
                   CGP_SEC2_COIL,
                   CGP_MS_COIL
            INTO V_CUSTOMER_NAME,
                 V_INPUT_THICKNESS,
                 V_INPUT_WIDTH,
                 V_INPUT_WEIGHT
            FROM T_GP2_PDI
            WHERE CGP_ID_COIL = MOTHER_COIL;

            IF DAUGHTER_COIL IS NOT NULL THEN
                V_NET_WEIGHT := ((V_INPUT_THICKNESS * (V_ACT_WIDTH + 4) * 7.85 * V_ACT_LENGTH) / 1000000);
                V_DAUGHTER_COIL_WEIGHT := V_NET_WEIGHT;
                FINAL_COIL_ID := DAUGHTER_COIL;
                H_STM_ID := 'INSERT DAUGHTER';
            ELSE
                FINAL_COIL_ID := MOTHER_COIL;
                H_STM_ID := 'INSERT MOTHER';
            END IF;

            INSERT INTO T_PDO_INFO (
                CGD_ID_COIL,
                CGD_MK_CUSTOMER,
                CGD_TS_START
            ) VALUES (
                FINAL_COIL_ID,
                V_CUSTOMER_NAME,
                V_CGD_TS_START_TIME
            );
        END IF;
    END IF;

    H_STM_ID := 'UPDATE RECOILER TRACKING';

    SELECT COIL_ID INTO V_CURR_COIL
    FROM T_EVENT_TRACKING
    WHERE ID_APP_TAG_EVENT = 'RECOILER';

    SELECT COIL_ID INTO V_NEXT_COIL
    FROM T_EVENT_TRACKING
    WHERE ID_APP_TAG_EVENT = 'WELD_AT_BR8';

    IF NVL(V_NEXT_COIL, 'NULL') != NVL(V_CURR_COIL, 'NULL') THEN
        UPDATE T_EVENT_TRACKING
        SET COIL_ID = V_NEXT_COIL
        WHERE ID_APP_TAG_EVENT = 'RECOILER';
    END IF;

    COMMIT;

EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error occurred at step: ' || H_STM_ID);
        DBMS_OUTPUT.PUT_LINE('Error message: ' || SQLERRM);
        COMMIT;
END PDO_GENERATION;