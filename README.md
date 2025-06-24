create or replace PROCEDURE PDO_GENERATION AS
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
    V_CGD_TS_END_TIME TIMESTAMP(6) := SYSDATE;
    V_CGD_WORK_DUR NUMBER := 0;
   V_CGD_ACT_THICKNESS NUMBER:=0;
    -- variables to capture all process parameters
    -- SELECT FROM PDI Table
     V_TDC_NUM VARCHAR2(10);
     V_GRADE VARCHAR2(10);
     V_INPUT_THICKNESS NUMBER :=0;
     V_Input_width number :=0;
     V_INPUT_WEIGHT NUMBER:=0;
     V_CUSTOMER_NAME VARCHAR(50);
    V_DAUGHTER_COIL_WEIGHT NUMBER :=0;
V_NET_WEIGHT NUMBER :=0;

     -- process paramete defined

      V_ENTRY_ACCU_TENSION_ACT NUMBER :=0;
      V_ENTRY_SPEED_ACT NUMBER :=0;
      V_EXIT_ACCU_TEN_ACT NUMBER :=0;
      V_POR_TENSION_ACT NUMBER :=0;
      V_BR2_TENSION_ACT NUMBER :=0;
      V_GL_POT_TEMP NUMBER :=0;
      V_RECOILER_TENSION_ACT NUMBER :=0;
      V_TLL_TENSION_ACT NUMBER :=0;
      V_POT_TENSION_ACT NUMBER :=0;
      V_PHF_ZONE_1_TEMP_ACT NUMBER :=0;
      V_PHF_ZONE_2_TEMP_ACT NUMBER :=0;
      V_PHF_ZONE_3_TEMP_ACT NUMBER :=0;
      V_RTF_ZONE_1_TEMP_ACT NUMBER :=0;
      V_SOAK_ZONE_1_TEMP_ACT NUMBER :=0;
      V_SOAK_ZONE_2_TEMP_ACT NUMBER :=0;
      V_JCF_ZONE_1_TEMP_ACT NUMBER :=0;
      V_JCF_ZONE_2_TEMP_ACT NUMBER :=0;
      V_JCF_ZONE_3_TEMP_ACT NUMBER :=0;
      V_RTF_ZONE_2_TEMP_ACT NUMBER :=0;
      V_RTF_ZONE_3_TEMP_ACT NUMBER :=0;
      V_RTF_ZONE_4_TEMP_ACT NUMBER :=0;

     V_ACT_LENGTH NUMBER :=0;
     V_ACT_WIDTH NUMBER :=0;
     V_ACT_WEIGHT NUMBER:=0;-- MEASURED WEIGHT
     V_CALCULATED_WEIGHT NUMBER :=0;
     V_CURR_COIL VARCHAR2(15);
     V_NEXT_COIL VARCHAR2(15);

BEGIN
    H_STM_ID := 'SELECT 1';

    -- Fetching MOTHER_COIL and ZERO_NUMBER
    SELECT COIL_ID, LENGTH(COIL_ID) - LENGTH(TRIM(TRAILING '0' FROM COIL_ID))
    INTO MOTHER_COIL, ZERO_NUMBER
    FROM T_EVENT_TRACKING
    WHERE ID_APP_TAG_EVENT = 'RECOILER';

    IF MOTHER_COIL IS NOT NULL AND MOTHER_COIL != 'NO_COIL' THEN


    SUB_COIL := SUBSTR(MOTHER_COIL, 1, LENGTH(MOTHER_COIL) - ZERO_NUMBER);

    -- Checking REC_SEC against LIMIT_TIME
    IF REC_SEC < LIMIT_TIME THEN
        H_STM_ID := 'SELECT 3';

        -- Counting occurrences of mother coil
        SELECT COUNT(CGD_ID_COIL_MTHR)
        INTO PART_NUM
        FROM T_PDO_INFO
        WHERE CGD_ID_COIL_MTHR = MOTHER_COIL;

        PART_NUM := PART_NUM + 1;

        -- Determine DAUGHTER_COIL based on ZERO_NUMBER
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


V_ACT_LENGTH:'780';
--End of code to fetch lengh

     
           
select DATE_TIME into V_CGD_TS_START_TIME FROM  ( select DATE_TIME, ROW_NUMBER() OVER (ORDER BY DATE_TIME DESC) AS rn from T_event_log where ID_APP_TAG='RECOILER' and VALUE=1)  WHERE rn = 1;
--- End length code-- 
            -- Calculate the duration in minutes and convert to NUMBER
            V_CGD_WORK_DUR := TO_NUMBER(
                EXTRACT(MINUTE FROM (V_CGD_TS_END_TIME - V_CGD_TS_START_TIME)) +
                EXTRACT(HOUR FROM (V_CGD_TS_END_TIME - V_CGD_TS_START_TIME)) * 60 +
                EXTRACT(DAY FROM (V_CGD_TS_END_TIME - V_CGD_TS_START_TIME)) * 24 * 60
            );

         
           
           
           --CALCLUTING SPEED SEPARATELY EXCLUDING 0 VALUES WHILE GETTING AVG
           SELECT  ROUND( NVL(AVG(ENTRY_SPEED_ACT),0),2) INTO  V_ENTRY_SPEED_ACT FROM 
           T_PERIODIC_VALUE_LOG WHERE (DATE_TIME BETWEEN V_CGD_TS_START_TIME AND V_CGD_TS_END_TIME) AND ENTRY_SPEED_ACT !=0 ;
           
 
              SELECT CGP_MK_CUSTOMER,
                     --CGP_SEC1_COIL,
                     CGP_SEC1_ORD,
                     CGP_SEC2_COIL,
                     CGP_MS_COIL

                     INTO
                     V_CUSTOMER_NAME,
                     V_INPUT_THICKNESS,
                     V_INPUT_WIDTH,
                     V_INPUT_WEIGHT

                     FROM T_GP2_PDI WHERE CGP_ID_COIL=MOTHER_COIL;

           INSERT INTO T_PDO_INFO (
    CGD_ID_COIL,                     -- Coil ID
    CGD_MK_CUSTOMER,                 -- Customer name
    CGD_TS_START,                    -- Start time
    CGD_TS_END,                      -- End time
    CGD_WORK_DUR,                    -- Work duration
    CGD_ID_COIL_MTHR,                -- Mother Coil ID
    CGD_SEC1_COIL,                   -- Input Thickness
    CGD_WIDTH,                       -- Input Width
    CGD_LN_COIL,                     -- Actual Length
    CGD_MS_ACTL,
    CGD_ACT_THICKNESS,                   -- Actual Thickness
      ACT_WIDTH,                      -- Actual Width
)
VALUES (
     DAUGHTER_COIL,                     -- Coil ID from the parent coil
    V_CUSTOMER_NAME,                   -- Customer name from PDI
    V_CGD_TS_START_TIME,                    -- Start time from the event log
    V_CGD_TS_END_TIME,                      -- End time from the event log
    V_CGD_WORK_DUR,                    -- Work duration calculated
    MOTHER_COIL,                     -- Mother Coil ID from the event tracking
    V_INPUT_THICKNESS,                 -- Input Thickness from PDI
    V_INPUT_WIDTH,                     -- Input Width from PDI
    V_ACT_LENGTH,                      -- Actual Length from process parameters
    V_ACT_WEIGHT,
     V_CGD_ACT_THICKNESS,                   -- Actual Thickness from process parameters
    V_INPUT_WIDTH,                      -- Actual Width from process parameter
    V_ENTRY_SPEED_ACT,                 -- Entry Speed Actual from process parameters
 

);
  

        END IF;

       
--        



    END IF;
    ELSE

        END IF;

    END IF;
  COMMIT;
EXCEPTION
    WHEN OTHERS THEN
        -- Log error details
        DBMS_OUTPUT.PUT_LINE('Error occurred at step: ' || H_STM_ID);
        DBMS_OUTPUT.PUT_LINE('Error message: ' || SQLERRM);

        -- Commit before ending (if necessary)
        COMMIT;
END PDO_GENERATION;
