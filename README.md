CREATE OR REPLACE PROCEDURE PDO_GENERATION_Test_17_11 AS
    MOTHER_COIL VARCHAR2(50);
    DAUGHTER_COIL VARCHAR2(50);
    PART_NUM INT := 0;
    ZERO_NUMBER INT := 0;
    REC_SEC NUMBER := 0;
    SUB_COIL VARCHAR2(20);
    LIMIT_TIME NUMBER := 300;
    H_STM_ID VARCHAR2(100) := NULL;

    -- Added variables
    V_CGD_TS_START_TIME TIMESTAMP(6) := SYSDATE;
    V_CGD_TS_END_TIME   TIMESTAMP(6) := SYSDATE;
    V_CGD_WORK_DUR NUMBER := 0;
    V_CGD_ACT_THICKNESS NUMBER := 0;

    -- SELECT FROM PDI Table
    V_INPUT_THICKNESS NUMBER := 0;
    V_INPUT_WIDTH NUMBER := 0;
    V_INPUT_WEIGHT NUMBER := 0;
    V_CUSTOMER_NAME VARCHAR2(50);

    V_ACT_LENGTH NUMBER := 0;
    V_ACT_WIDTH NUMBER := 0;
    V_ACT_WEIGHT NUMBER := 0;

    V_DAUGHTER_COIL_WEIGHT NUMBER := 0;
    V_NET_WEIGHT NUMBER := 0;

    -- Auto GR variables
    v_INPUT_WEIGHT_MB NUMBER(8,2) := 0;
    v_DGTR_BATCH_COMBINED_WGT NUMBER(8,2) := 0;
    v_MAX_ALLOWED_WGT NUMBER(8,2) := 0;

    -- SO Coating
    V_SO_COAT NUMBER(8,3) := 0;

BEGIN
    H_STM_ID := 'STEP 1';

    -- Fetch mother coil
    SELECT COIL_ID,
           LENGTH(COIL_ID) - LENGTH(TRIM(TRAILING '0' FROM COIL_ID))
    INTO MOTHER_COIL, ZERO_NUMBER
    FROM T_EVENT_TRACKING
    WHERE ID_APP_TAG_EVENT = 'RECOILER';

    IF MOTHER_COIL IS NOT NULL AND MOTHER_COIL != 'NO_COIL' THEN

        SUB_COIL := SUBSTR(MOTHER_COIL, 1, LENGTH(MOTHER_COIL) - ZERO_NUMBER);

        IF REC_SEC < LIMIT_TIME THEN
            H_STM_ID := 'STEP 2';

            SELECT COUNT(CGD_ID_COIL_MTHR)
            INTO PART_NUM
            FROM T_PDO_INFO
            WHERE CGD_ID_COIL_MTHR = MOTHER_COIL;

            PART_NUM := PART_NUM + 1;

            -- Generate daughter coil
            IF ZERO_NUMBER = 1 THEN
                DAUGHTER_COIL := SUB_COIL || TO_CHAR(PART_NUM);
            ELSIF ZERO_NUMBER = 2 THEN
                DAUGHTER_COIL := SUB_COIL || TO_CHAR(PART_NUM * 10);
            ELSIF ZERO_NUMBER = 3 THEN
                DAUGHTER_COIL := SUB_COIL || TO_CHAR(PART_NUM * 100);
            ELSIF ZERO_NUMBER = 4 THEN
                DAUGHTER_COIL := SUB_COIL || '0' || TO_CHAR(PART_NUM * 100);
            ELSIF ZERO_NUMBER = 5 THEN
                DAUGHTER_COIL := SUB_COIL || '00' || TO_CHAR(PART_NUM * 100);
            END IF;

            -- Fetch length
            SELECT ROUND(NVL(MAX(ACT_LENGTH), 0), 2) + 7
            INTO V_ACT_LENGTH
            FROM T_PERIODIC_VALUE_LOG
            WHERE DATE_TIME BETWEEN (V_CGD_TS_END_TIME - INTERVAL '5' MINUTE) AND V_CGD_TS_END_TIME;

            -- Fix NULL daughter coil
            IF DAUGHTER_COIL IS NULL THEN
                DAUGHTER_COIL := MOTHER_COIL;
            END IF;

            -- Fetch recoiler start time
            SELECT DATE_TIME
            INTO V_CGD_TS_START_TIME
            FROM (
                SELECT DATE_TIME FROM T_EVENT_LOG
                WHERE ID_APP_TAG='RECOILER' AND VALUE=1
                ORDER BY DATE_TIME DESC
            ) WHERE ROWNUM = 1;

            -- Fetch coating
            SELECT LN_AZ_COAT
            INTO V_SO_COAT
            FROM (
                SELECT
                    CASE 
                        WHEN SUBSTR(CGP_CD_COAT, 1, 1) = 'Z' THEN
                            CASE WHEN TO_NUMBER(SUBSTR(CGP_CD_COAT, 2, 5)) <> 0
                                THEN NVL(TO_NUMBER(SUBSTR(CGP_CD_COAT, 2, 5)), 1) / 7.14 / 1000
                            END
                        WHEN SUBSTR(CGP_CD_COAT, 1, 1) = 'A' THEN
                            CASE WHEN TO_NUMBER(SUBSTR(CGP_CD_COAT, 3, 5)) <> 0
                                THEN NVL(TO_NUMBER(SUBSTR(CGP_CD_COAT, 3, 5)), 1) / 3.78 / 1000
                            END
                    END AS LN_AZ_COAT
                FROM T_GP2_PDI
                WHERE CGP_ID_COIL = MOTHER_COIL
                ORDER BY CGP_TOC DESC
            )
            WHERE ROWNUM = 1;

            -- Fetch PDI data
            SELECT CGP_MK_CUSTOMER, CGP_SEC1_ORD, CGP_SEC2_COIL, CGP_MS_COIL
            INTO V_CUSTOMER_NAME, V_INPUT_THICKNESS, V_INPUT_WIDTH, V_INPUT_WEIGHT
            FROM T_GP2_PDI
            WHERE CGP_ID_COIL = MOTHER_COIL;

            -- Compute weight
            V_NET_WEIGHT :=
                ((V_INPUT_THICKNESS * V_ACT_WIDTH * V_ACT_LENGTH * 7.85)
                + ((V_ACT_WIDTH * V_ACT_LENGTH * V_SO_COAT) / 1000000))
                / 1000000;

            V_DAUGHTER_COIL_WEIGHT := V_NET_WEIGHT;
            v_DGTR_BATCH_COMBINED_WGT := v_DGTR_BATCH_COMBINED_WGT + V_DAUGHTER_COIL_WEIGHT;

            v_MAX_ALLOWED_WGT := V_INPUT_WEIGHT * 1.10;

            -- Insert PDO
            IF v_DGTR_BATCH_COMBINED_WGT <= v_MAX_ALLOWED_WGT THEN
                INSERT INTO T_PDO_INFO (
                    CGD_ID_COIL,
                    CGD_MK_CUSTOMER,
                    CGD_TS_START,
                    CGD_TS_END,
                    CGD_WORK_DUR,
                    CGD_ID_COIL_MTHR,
                    CGD_SEC1_COIL,
                    CGD_WIDTH,
                    CGD_LN_COIL,
                    CGD_MS_CAL,
                    CGD_MS_ACTL
                )
                VALUES (
                    DAUGHTER_COIL,
                    V_CUSTOMER_NAME,
                    V_CGD_TS_START_TIME,
                    V_CGD_TS_END_TIME,
                    V_CGD_WORK_DUR,
                    MOTHER_COIL,
                    V_INPUT_THICKNESS,
                    V_INPUT_WIDTH,
                    V_ACT_LENGTH,
                    V_DAUGHTER_COIL_WEIGHT,
                    V_DAUGHTER_COIL_WEIGHT
                );
            END IF;

        END IF;
    END IF;

    COMMIT;
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error at: ' || H_STM_ID);
        DBMS_OUTPUT.PUT_LINE('SQLERRM: ' || SQLERRM);
        COMMIT;
END PDO_GENERATION_Test_17_11;
/