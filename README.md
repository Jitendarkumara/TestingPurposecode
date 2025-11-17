CREATE OR REPLACE PROCEDURE PDO_GENERATION_Test_17_11 AS
    -- Coil identifiers
    MOTHER_COIL            VARCHAR2(50);
    DAUGHTER_COIL          VARCHAR2(50);
    PART_NUM               PLS_INTEGER := 0;
    ZERO_NUMBER            PLS_INTEGER := 0;
    SUB_COIL               VARCHAR2(50);
    LIMIT_TIME             NUMBER := 300; -- seconds
    H_STM_ID               VARCHAR2(200) := NULL;

    -- timestamps and durations
    V_CGD_TS_START_TIME    TIMESTAMP(6);
    V_CGD_TS_END_TIME      TIMESTAMP(6);
    V_CGD_WORK_DUR         NUMBER := 0; -- minutes

    -- PDI/process variables
    V_TDC_NUM              VARCHAR2(10);
    V_GRADE                VARCHAR2(10);
    V_INPUT_THICKNESS      NUMBER := 0;
    V_Input_width          NUMBER := 0;
    V_INPUT_WEIGHT         NUMBER := 0;
    V_CUSTOMER_NAME        VARCHAR2(50);

    V_DAUGHTER_COIL_WEIGHT NUMBER := 0;
    V_NET_WEIGHT           NUMBER := 0;

    -- process parameters (initialized)
    V_ENTRY_ACCU_TENSION_ACT  NUMBER := 0;
    V_ENTRY_SPEED_ACT         NUMBER := 0;
    V_EXIT_ACCU_TEN_ACT       NUMBER := 0;
    V_POR_TENSION_ACT         NUMBER := 0;
    V_BR2_TENSION_ACT         NUMBER := 0;
    V_GL_POT_TEMP             NUMBER := 0;
    V_RECOILER_TENSION_ACT    NUMBER := 0;
    V_TLL_TENSION_ACT         NUMBER := 0;
    V_POT_TENSION_ACT         NUMBER := 0;
    V_PHF_ZONE_1_TEMP_ACT     NUMBER := 0;
    V_PHF_ZONE_2_TEMP_ACT     NUMBER := 0;
    V_PHF_ZONE_3_TEMP_ACT     NUMBER := 0;
    V_RTF_ZONE_1_TEMP_ACT     NUMBER := 0;
    V_SOAK_ZONE_1_TEMP_ACT    NUMBER := 0;
    V_SOAK_ZONE_2_TEMP_ACT    NUMBER := 0;
    V_JCF_ZONE_1_TEMP_ACT     NUMBER := 0;
    V_JCF_ZONE_2_TEMP_ACT     NUMBER := 0;
    V_JCF_ZONE_3_TEMP_ACT     NUMBER := 0;
    V_RTF_ZONE_2_TEMP_ACT     NUMBER := 0;
    V_RTF_ZONE_3_TEMP_ACT     NUMBER := 0;
    V_RTF_ZONE_4_TEMP_ACT     NUMBER := 0;

    V_ACT_LENGTH              NUMBER := 0;
    V_ACT_WIDTH               NUMBER := 0;
    V_ACT_WEIGHT              NUMBER := 0; -- measured weight
    V_CGD_ACT_THICKNESS       NUMBER := 0;

    -- auto GR management
    v_INPUT_WEIGHT_MB         NUMBER(12,2) := 0; -- mother batch input weight
    v_DGTR_BATCH_COMBINED_WGT NUMBER(12,2) := 0;
    v_MAX_ALLOWED_WGT         NUMBER(12,2) := 0;

    -- SO Coating
    V_SO_COAT                 NUMBER := 0;

    -- temporary current/next coil
    V_CURR_COIL               VARCHAR2(50);
    V_NEXT_COIL               VARCHAR2(50);

    -- REC_SEC (kept as 0 unless you set it elsewhere)
    REC_SEC                   NUMBER := 0;

BEGIN
    H_STM_ID := 'START';

    -- Initialize timestamps with current server timestamp as fallback
    V_CGD_TS_START_TIME := SYSTIMESTAMP;
    V_CGD_TS_END_TIME   := SYSTIMESTAMP;

    -- Get latest MOTHER_COIL and number of trailing zeros
    H_STM_ID := 'FETCH_MOTHER_COIL';
    SELECT COIL_ID,
           NVL(LENGTH(COIL_ID) - LENGTH(TRIM(TRAILING '0' FROM COIL_ID)), 0)
    INTO   MOTHER_COIL, ZERO_NUMBER
    FROM   T_EVENT_TRACKING
    WHERE  ID_APP_TAG_EVENT = 'RECOILER'
    AND    ROWNUM = 1;  -- assume one row

    IF MOTHER_COIL IS NOT NULL AND MOTHER_COIL <> 'NO_COIL' THEN
        H_STM_ID := 'CALC_SUB_COIL';
        SUB_COIL := SUBSTR(MOTHER_COIL, 1, GREATEST(1, LENGTH(MOTHER_COIL) - ZERO_NUMBER));

        -- if REC_SEC isn't set elsewhere it will be 0 and this condition will pass
        IF NVL(REC_SEC,0) < LIMIT_TIME THEN
            H_STM_ID := 'COUNT_PARTS';
            SELECT COUNT(CGD_ID_COIL_MTHR) INTO PART_NUM
            FROM   T_PDO_INFO
            WHERE  CGD_ID_COIL_MTHR = MOTHER_COIL;

            PART_NUM := NVL(PART_NUM,0) + 1;

            -- build daughter coil based on trailing zero count
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
            ELSE
                -- default fallback
                DAUGHTER_COIL := SUB_COIL || TO_CHAR(PART_NUM);
            END IF;

            -- fetch end-of-coil length measurement (last 5 minutes prior to end time)
            H_STM_ID := 'FETCH_ACT_LENGTH';
            SELECT ROUND(NVL(MAX(ACT_LENGTH),0),2) + 7
            INTO   V_ACT_LENGTH
            FROM   T_PERIODIC_VALUE_LOG
            WHERE  DATE_TIME BETWEEN (V_CGD_TS_END_TIME - INTERVAL '5' MINUTE) AND V_CGD_TS_END_TIME;

            -- if DAUGHTER_COIL somehow null, set to mother
            IF DAUGHTER_COIL IS NULL THEN
                DAUGHTER_COIL := MOTHER_COIL;
            END IF;

            -- fetch recoiler start time (last VALUE=1 event)
            H_STM_ID := 'FETCH_START_TIME';
            SELECT DATE_TIME
            INTO   V_CGD_TS_START_TIME
            FROM (
                SELECT DATE_TIME
                FROM   T_EVENT_LOG
                WHERE  ID_APP_TAG = 'RECOILER' AND VALUE = 1
                ORDER  BY DATE_TIME DESC
            ) WHERE ROWNUM = 1;

            -- fetch recoiler end time (last VALUE=0 event) - fallback to SYSTIMESTAMP if not found
            H_STM_ID := 'FETCH_END_TIME';
            BEGIN
                SELECT DATE_TIME
                INTO   V_CGD_TS_END_TIME
                FROM (
                    SELECT DATE_TIME
                    FROM   T_EVENT_LOG
                    WHERE  ID_APP_TAG = 'RECOILER' AND VALUE = 0
                    ORDER  BY DATE_TIME DESC
                ) WHERE ROWNUM = 1;
            EXCEPTION
                WHEN NO_DATA_FOUND THEN
                    -- keep V_CGD_TS_END_TIME as SYSTIMESTAMP fallback
                    NULL;
            END;

            -- Fetch SO coating from T_GP2_PDI: take latest CGP_TOC row for this coil
            H_STM_ID := 'FETCH_SO_COAT';
            BEGIN
                SELECT LN_AZ_COAT
                INTO   V_SO_COAT
                FROM (
                    SELECT
                        CASE
                            WHEN NVL(CGP_CD_COAT,' ') LIKE 'Z%' AND REGEXP_LIKE(SUBSTR(CGP_CD_COAT,2,5),'^\d+$')
                                THEN TO_NUMBER(SUBSTR(CGP_CD_COAT,2,5)) / 7.14 / 1000
                            WHEN NVL(CGP_CD_COAT,' ') LIKE 'A%' AND REGEXP_LIKE(SUBSTR(CGP_CD_COAT,3,5),'^\d+$')
                                THEN TO_NUMBER(SUBSTR(CGP_CD_COAT,3,5)) / 3.78 / 1000
                            ELSE NULL
                        END AS LN_AZ_COAT
                    FROM T_GP2_PDI
                    WHERE CGP_ID_COIL = MOTHER_COIL
                    ORDER BY CGP_TOC DESC
                )
                WHERE ROWNUM = 1;
            EXCEPTION
                WHEN NO_DATA_FOUND THEN
                    V_SO_COAT := 0;
                WHEN OTHERS THEN
                    -- in case of bad data (non-numeric) set to 0
                    V_SO_COAT := 0;
            END;

            -- compute work duration in minutes (safe: if end < start, keep 0)
            H_STM_ID := 'CALC_WORK_DUR';
            IF V_CGD_TS_END_TIME >= V_CGD_TS_START_TIME THEN
                V_CGD_WORK_DUR := TO_NUMBER(
                    EXTRACT(MINUTE FROM (V_CGD_TS_END_TIME - V_CGD_TS_START_TIME))
                    + EXTRACT(HOUR FROM (V_CGD_TS_END_TIME - V_CGD_TS_START_TIME)) * 60
                    + EXTRACT(DAY FROM (V_CGD_TS_END_TIME - V_CGD_TS_START_TIME)) * 24 * 60
                );
            ELSE
                V_CGD_WORK_DUR := 0;
            END IF;

            -- FETCHING AVG OF PROCESS PARAMETERS AND MAX/MIN where required
            H_STM_ID := 'FETCH_PERIODIC_AVGS';
            BEGIN
                SELECT
                  ROUND(NVL(AVG(ENTRY_ACCU_TENSION_ACT),0),2),
                  ROUND(NVL(AVG(ENTRY_SPEED_ACT),0),2),
                  ROUND(NVL(AVG(EXIT_ACCU_TEN_ACT),0),2),
                  ROUND(NVL(AVG(POR_TENSION_ACT),0),2),
                  ROUND(NVL(AVG(BR2_TENSION_ACT),0),2),
                  ROUND(NVL(AVG(GL_POT_TEMP),0),2),
                  ROUND(NVL(AVG(RECOILER_TENSION_ACT),0),2),
                  ROUND(NVL(AVG(TLL_TENSION_ACT),0),2),
                  ROUND(NVL(AVG(POT_TENSION_ACT),0),2),
                  ROUND(NVL(AVG(PHF_ZONE_1_TEMP_ACT),0),2),
                  ROUND(NVL(AVG(PHF_ZONE_2_TEMP_ACT),0),2),
                  ROUND(NVL(AVG(PHF_ZONE_3_TEMP_ACT),0),2),
                  ROUND(NVL(AVG(RTF_ZONE_1_TEMP_ACT),0),2),
                  ROUND(NVL(AVG(SOAK_ZONE_1_TEMP_ACT),0),2),
                  ROUND(NVL(AVG(SOAK_ZONE_2_TEMP_ACT),0),2),
                  ROUND(NVL(AVG(JCF_ZONE_1_TEMP_ACT),0),2),
                  ROUND(NVL(AVG(JCF_ZONE_2_TEMP_ACT),0),2),
                  ROUND(NVL(AVG(JCF_ZONE_3_TEMP_ACT),0),2),
                  ROUND(NVL(AVG(RTF_ZONE_2_TEMP_ACT),0),2),
                  ROUND(NVL(AVG(RTF_ZONE_3_TEMP_ACT),0),2),
                  ROUND(NVL(AVG(RTF_ZONE_4_TEMP_ACT),0),2),
                  ROUND(NVL(MAX(ACT_WIDTH),0),2),
                  ROUND(NVL(AVG(ACT_WEIGHT),0),2),
                  ROUND(NVL(MAX(ACT_THICKNESS),0),2)
                INTO
                  V_ENTRY_ACCU_TENSION_ACT,
                  V_ENTRY_SPEED_ACT,
                  V_EXIT_ACCU_TEN_ACT,
                  V_POR_TENSION_ACT,
                  V_BR2_TENSION_ACT,
                  V_GL_POT_TEMP,
                  V_RECOILER_TENSION_ACT,
                  V_TLL_TENSION_ACT,
                  V_POT_TENSION_ACT,
                  V_PHF_ZONE_1_TEMP_ACT,
                  V_PHF_ZONE_2_TEMP_ACT,
                  V_PHF_ZONE_3_TEMP_ACT,
                  V_RTF_ZONE_1_TEMP_ACT,
                  V_SOAK_ZONE_1_TEMP_ACT,
                  V_SOAK_ZONE_2_TEMP_ACT,
                  V_JCF_ZONE_1_TEMP_ACT,
                  V_JCF_ZONE_2_TEMP_ACT,
                  V_JCF_ZONE_3_TEMP_ACT,
                  V_RTF_ZONE_2_TEMP_ACT,
                  V_RTF_ZONE_3_TEMP_ACT,
                  V_RTF_ZONE_4_TEMP_ACT,
                  V_ACT_WIDTH,
                  V_ACT_WEIGHT,
                  V_CGD_ACT_THICKNESS
                FROM T_PERIODIC_VALUE_LOG
                WHERE DATE_TIME BETWEEN V_CGD_TS_START_TIME AND V_CGD_TS_END_TIME;
            EXCEPTION
                WHEN NO_DATA_FOUND THEN
                    -- defaults already set to 0
                    NULL;
            END;

            -- fetch PDI master values for the mother coil (one row expected)
            H_STM_ID := 'FETCH_PDI_MASTER';
            BEGIN
                SELECT CGP_MK_CUSTOMER,
                       CGP_SEC1_ORD,
                       CGP_SEC2_COIL,
                       CGP_MS_COIL
                INTO   V_CUSTOMER_NAME,
                       V_INPUT_THICKNESS,
                       V_INPUT_WIDTH,
                       V_INPUT_WEIGHT
                FROM   T_GP2_PDI
                WHERE  CGP_ID_COIL = MOTHER_COIL
                AND    ROWNUM = 1;
            EXCEPTION
                WHEN NO_DATA_FOUND THEN
                    V_CUSTOMER_NAME := NULL;
                    V_INPUT_THICKNESS := NVL(V_INPUT_THICKNESS,0);
                    V_INPUT_WIDTH := NVL(V_INPUT_WIDTH,0);
                    V_INPUT_WEIGHT := NVL(V_INPUT_WEIGHT,0);
            END;

            -- Calculate net weight: convert units carefully and guard NULLs
            H_STM_ID := 'CALC_NET_WEIGHT';
            V_NET_WEIGHT :=
                ( NVL(V_INPUT_THICKNESS,0) * NVL(V_ACT_WIDTH,0) * NVL(V_ACT_LENGTH,0) * 7.85
                  + (NVL(V_ACT_WIDTH,0) * NVL(V_ACT_LENGTH,0) * NVL(V_SO_COAT,0)) / 1000000
                ) / 1000000;

            V_DAUGHTER_COIL_WEIGHT := V_NET_WEIGHT;
            v_DGTR_BATCH_COMBINED_WGT := NVL(v_DGTR_BATCH_COMBINED_WGT,0) + NVL(V_DAUGHTER_COIL_WEIGHT,0);

            v_MAX_ALLOWED_WGT := NVL(V_INPUT_WEIGHT,0) * 1.10;

            -- insert PDO row only if under weight limit
            IF v_DGTR_BATCH_COMBINED_WGT <= v_MAX_ALLOWED_WGT THEN
                H_STM_ID := 'INSERT_PDO_INFO';
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
                    CGD_MS_ACTL,
                    CGD_TAS_FURN_TEN,
                    CGD_TLL_TENSION,
                    CGD_TAS_EN_ACCTEN,
                    CGD_TAS_POR1_TEN,
                    CGD_TAS_POR2_TEN,
                    CGD_TAS_EX_ACCTEN,
                    CGD_TAS_SPM_ENT_TENSION,
                    CGD_TAS_SPM_EXT_TENSION,
                    CGD_PHF_ZON1_TMP,
                    CGD_PHF_ZON2_TMP,
                    CGD_PHF_ZON3_TMP,
                    CGD_RTF_ZON1_TMP,
                    CGD_RTF_ZON2_TMP,
                    CGD_RTF_ZON3_TMP,
                    CGD_HBR_HEATER_TMP,
                    CGD_JCF_HEATER_ZON1_TMP,
                    CGD_JCF_HEATER_ZON2_TMP,
                    CGD_JCF_HEATER_ZON3_TMP,
                    CGD_SOAK_ZON1_TMP,
                    CGD_SOAK_ZON2_TMP,
                    CGD_ACT_THICKNESS,
                    CGD_ACT_WIDTH,
                    CGD_RTF_ZON4_TMP,
                    RECOILER_TEN_ACT,
                    PROCESS_SPEED,
                    POT_TEN_ACT,
                    GL_POT_TEMP,
                    CGD_ACT_LENGTH
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
                    V_DAUGHTER_COIL_WEIGHT,
                    V_BR2_TENSION_ACT,
                    V_TLL_TENSION_ACT,
                    V_ENTRY_ACCU_TENSION_ACT,
                    V_POR_TENSION_ACT,
                    V_POR_TENSION_ACT,
                    V_EXIT_ACCU_TEN_ACT,
                    0, -- special entry tension
                    0, -- special exit tension
                    V_PHF_ZONE_1_TEMP_ACT,
                    V_PHF_ZONE_2_TEMP_ACT,
                    V_PHF_ZONE_3_TEMP_ACT,
                    V_RTF_ZONE_1_TEMP_ACT,
                    V_RTF_ZONE_2_TEMP_ACT,
                    V_RTF_ZONE_3_TEMP_ACT,
                    0, -- heater temp placeholder
                    V_JCF_ZONE_1_TEMP_ACT,
                    V_JCF_ZONE_2_TEMP_ACT,
                    V_JCF_ZONE_3_TEMP_ACT,
                    V_SOAK_ZONE_1_TEMP_ACT,
                    V_SOAK_ZONE_2_TEMP_ACT,
                    V_CGD_ACT_THICKNESS,
                    V_ACT_WIDTH,
                    V_RTF_ZONE_4_TEMP_ACT,
                    V_RECOILER_TENSION_ACT,
                    V_ENTRY_SPEED_ACT,
                    V_POT_TENSION_ACT,
                    V_GL_POT_TEMP,
                    V_ACT_LENGTH
                );
            END IF;

        END IF; -- REC_SEC < LIMIT_TIME
    END IF; -- mother coil exists

    -- Finally update event tracking with next coil if different
    H_STM_ID := 'UPDATE_EVENT_TRACKING';
    BEGIN
        SELECT COIL_ID INTO V_CURR_COIL FROM T_EVENT_TRACKING WHERE ID_APP_TAG_EVENT='RECOILER' AND ROWNUM=1;
    EXCEPTION WHEN NO_DATA_FOUND THEN V_CURR_COIL := NULL; END;

    BEGIN
        SELECT COIL_ID INTO V_NEXT_COIL FROM T_EVENT_TRACKING WHERE ID_APP_TAG_EVENT='WELD_AT_BR8' AND ROWNUM=1;
    EXCEPTION WHEN NO_DATA_FOUND THEN V_NEXT_COIL := NULL; END;

    IF NVL(V_NEXT_COIL,'NULL') != NVL(V_CURR_COIL,'NULL') THEN
        UPDATE T_EVENT_TRACKING SET COIL_ID = V_NEXT_COIL WHERE ID_APP_TAG_EVENT='RECOILER';
    END IF;

    COMMIT;
EXCEPTION
    WHEN OTHERS THEN
        -- print position and error and rollback
        DBMS_OUTPUT.PUT_LINE('Error occurred at step: ' || H_STM_ID);
        DBMS_OUTPUT.PUT_LINE('Error message: ' || SQLERRM);
        ROLLBACK;
        RAISE; -- re-raise so caller knows about the failure
END PDO_GENERATION_Test_17_11;
/