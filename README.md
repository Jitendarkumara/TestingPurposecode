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

    IF MOTHER_COIL IS NOT NULL AND MOTHER_COIL != 'NO_COIL' THEN -- if no1


    SUB_COIL := SUBSTR(MOTHER_COIL, 1, LENGTH(MOTHER_COIL) - ZERO_NUMBER);

    -- Checking REC_SEC against LIMIT_TIME
    IF REC_SEC < LIMIT_TIME THEN --if no2
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


--Fetching Length from T_periodic table from the average of last 5 minut of end of the calio  this code added by jitu on 11/06/25

     select  ROUND(NVL( MAX( ACT_LENGTH),0),2) into V_ACT_LENGTH
 --FROM T_PERIODIC_VALUE_LOG WHERE DATE_TIME BETWEEN (V_CGD_TS_START_TIME + INTERVAL '1' MINUTE) AND V_CGD_TS_END_TIME;
 FROM T_PERIODIC_VALUE_LOG WHERE DATE_TIME BETWEEN (V_CGD_TS_END_TIME - INTERVAL '5' MINUTE) AND V_CGD_TS_END_TIME;

--End of code to fetch lengh

        IF DAUGHTER_COIL IS NULL THEN ---no. 3
            H_STM_ID := 'INSERT 1';

            -- Calculate P_START_TIME and P_END_TIME --commented by jitu
           --- SELECT MAX(DATE_TIME)
          --  INTO V_CGD_TS_START_TIME
           -- FROM T_EVENT_LOG
          --  WHERE ID_APP_TAG = 'L1_POR1_SEL' OR ID_APP_TAG = 'L1_POR2_SEL';
 ----Here we are fetching starting from T_event_Lof last recoiler signal time            
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
           
             -- FETCHING AVG OF PROCESS PARAMETER BASED ON START END TIME

             SELECT ROUND(NVL(AVG(ENTRY_ACCU_TENSION_ACT),0),2),
                --   ROUND( NVL(AVG(ENTRY_SPEED_ACT),0),2),
                   ROUND( NVL(AVG( EXIT_ACCU_TEN_ACT ),0),2),
                   ROUND(NVL(AVG(POR_TENSION_ACT),0),2),
                   ROUND(NVL(AVG(BR2_TENSION_ACT),0),2),
                   ROUND(NVL(AVG(GL_POT_TEMP),0),2),
                   ROUND(NVL(AVG( RECOILER_TENSION_ACT),0),2),
                   ROUND(NVL( AVG(TLL_TENSION_ACT),0),2),
                   ROUND(NVL( AVG(POT_TENSION_ACT),0),2),
                   ROUND(NVL( AVG(PHF_ZONE_1_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(PHF_ZONE_2_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(PHF_ZONE_3_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(RTF_ZONE_1_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(SOAK_ZONE_1_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(SOAK_ZONE_2_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(JCF_ZONE_1_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(JCF_ZONE_2_TEMP_ACT),0),2),
                   ROUND(NVL( AVG( JCF_ZONE_3_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(RTF_ZONE_2_TEMP_ACT),0),2),
                   ROUND(NVL( AVG( RTF_ZONE_3_TEMP_ACT),0),2),
                   ROUND(NVL( AVG( RTF_ZONE_4_TEMP_ACT),0),2),
                  -- ROUND(NVL( MAX(END_LENGTH),0),2),
                   -- ROUND(NVL( MAX(ACT_LENGTH),0),2),--commented by jitu on 11/06
                 --  ROUND(NVL( MAX(ACT_WIDTH),0),2),
                   ROUND(NVL( AVG(ACT_WEIGHT),0),2),-- MEASURED WEIGHT-- MEASURED WEIGHT
                   ROUND(NVL(MAX(ACT_THICKNESS),0),2)
                   INTO
                     V_ENTRY_ACCU_TENSION_ACT,
                  --   V_ENTRY_SPEED_ACT,
                     V_EXIT_ACCU_TEN_ACT,
                     V_POR_TENSION_ACT,
                     V_BR2_TENSION_ACT,
                     V_GL_POT_TEMP,
                     V_RECOILER_TENSION_ACT,
                     V_TLL_TENSION_ACT,
                     V_POT_TENSION_ACT,
                     V_PHF_ZONE_1_TEMP_ACT ,
                     V_PHF_ZONE_2_TEMP_ACT,
                     V_PHF_ZONE_3_TEMP_ACT,
                     V_RTF_ZONE_1_TEMP_ACT,
                     V_SOAK_ZONE_1_TEMP_ACT ,
                     V_SOAK_ZONE_2_TEMP_ACT,
                     V_JCF_ZONE_1_TEMP_ACT,
                     V_JCF_ZONE_2_TEMP_ACT,
                     V_JCF_ZONE_3_TEMP_ACT ,
                     V_RTF_ZONE_2_TEMP_ACT,
                     V_RTF_ZONE_3_TEMP_ACT,
                     V_RTF_ZONE_4_TEMP_ACT ,
                    -- V_ACT_LENGTH, -- commented by jitu on 11/06
                   --  V_ACT_WIDTH ,
                     V_ACT_WEIGHT,
                     V_CGD_ACT_THICKNESS
-- commented by jitu on 10/06/2025  
 FROM T_PERIODIC_VALUE_LOG WHERE DATE_TIME BETWEEN V_CGD_TS_START_TIME AND V_CGD_TS_END_TIME;
 --FROM T_PERIODIC_VALUE_LOG WHERE DATE_TIME BETWEEN (V_CGD_TS_START_TIME + INTERVAL '1' MINUTE) AND V_CGD_TS_END_TIME;
     ---  Daughter Coil Wt =SO Thickness x (SO Width + 4 mm) x Density (7.85) x Coil Length form Recoiler)/1000000  + Paint Gain ( Net coil wt x 0.38 /1000)

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
    CGD_TAS_FURN_TEN,                -- BR2 Tension Actual
    CGD_TLL_TENSION,                 -- TLL Tension Actual
    CGD_TAS_EN_ACCTEN,               -- Entry Accumulation Tension Actual
    CGD_TAS_POR1_TEN,                -- POR Tension Actual (1)
    CGD_TAS_POR2_TEN,                -- POR Tension Actual (2)
    CGD_TAS_EX_ACCTEN,               -- Exit Accumulation Tension Actual
    CGD_TAS_SPM_ENT_TENSION,         -- Special Tension (Entry, set to 0)
    CGD_TAS_SPM_EXT_TENSION,         -- Special Tension (Exit, set to 0)
    CGD_PHF_ZON1_TMP,                -- PHF Zone 1 Temperature Actual
    CGD_PHF_ZON2_TMP,
    CGD_PHF_ZON3_TMP,                -- PHF Zone 3 Temperature Actual
    CGD_RTF_ZON1_TMP,                -- RTF Zone 1 Temperature Actual
    CGD_RTF_ZON2_TMP,                -- RTF Zone 2 Temperature Actual
    CGD_RTF_ZON3_TMP,                -- RTF Zone 3 Temperature Actual
    CGD_HBR_HEATER_TMP,              -- Heater Temp (set to 0 for now)
    CGD_JCF_HEATER_ZON1_TMP,         -- JCF Zone 1 Temperature Actual
    CGD_JCF_HEATER_ZON2_TMP,         -- JCF Zone 2 Temperature Actual
    CGD_JCF_HEATER_ZON3_TMP,         -- JCF Zone 3 Temperature Actual
    CGD_SOAK_ZON1_TMP,               -- Soak Zone 1 Temperature Actual
    CGD_SOAK_ZON2_TMP,
    CGD_ACT_THICKNESS,                   -- Actual Thickness
      ACT_WIDTH,                      -- Actual Width
    CGD_RTF_ZON4_TMP,                -- RTF Zone 4 Temperature Actual
    RECOILER_TEN_ACT,                -- Recoiler Tension Actual
    PROCESS_SPEED,                   -- Entry Speed Actual
    POT_TEN_ACT,                     -- Pot Tension Actual
    GL_POT_TEMP   


)
VALUES (
     MOTHER_COIL,                     -- Coil ID from the parent coil
    V_CUSTOMER_NAME,                   -- Customer name from PDI
    V_CGD_TS_START_TIME,                    -- Start time from the event log
    V_CGD_TS_END_TIME,                      -- End time from the event log
    V_CGD_WORK_DUR,                    -- Work duration calculated
    MOTHER_COIL,                     -- Mother Coil ID from the event tracking
    V_INPUT_THICKNESS,                 -- Input Thickness from PDI
    V_INPUT_WIDTH,                     -- Input Width from PDI
    V_ACT_LENGTH,                      -- Actual Length from process parameters
    V_ACT_WEIGHT,
    V_BR2_TENSION_ACT,                 -- BR2 Tension Actual from process parameters
    V_TLL_TENSION_ACT,                 -- TLL Tension Actual from process parameters
    V_ENTRY_ACCU_TENSION_ACT,          -- Entry Accumulation Tension Actual from process parameters
    V_POR_TENSION_ACT,                 -- POR Tension Actual (1) from process parameters
    V_POR_TENSION_ACT,                 -- POR Tension Actual (2) from process parameters
    V_EXIT_ACCU_TEN_ACT,               -- Exit Accumulation Tension Actual from process parameters
    0,                               -- Special Tension (Entry), set to 0
    0,                               -- Special Tension (Exit), set to 0
    V_PHF_ZONE_1_TEMP_ACT,             -- PHF Zone 1 Temperature Actual from process parameters
    V_PHF_ZONE_2_TEMP_ACT,
    V_PHF_ZONE_3_TEMP_ACT,             -- PHF Zone 3 Temperature Actual from process parameters
    V_RTF_ZONE_1_TEMP_ACT,             -- RTF Zone 1 Temperature Actual from process parameters
    V_RTF_ZONE_2_TEMP_ACT,             -- RTF Zone 2 Temperature Actual from process parameters
    V_RTF_ZONE_3_TEMP_ACT,             -- RTF Zone 3 Temperature Actual from process parameters
    0,                               -- Heater Temp, set to 0 for now
    V_JCF_ZONE_1_TEMP_ACT,             -- JCF Zone 1 Temperature Actual from process parameters
    V_JCF_ZONE_2_TEMP_ACT,             -- JCF Zone 2 Temperature Actual from process parameters
    V_JCF_ZONE_3_TEMP_ACT,             -- JCF Zone 3 Temperature Actual from process parameters
    V_SOAK_ZONE_1_TEMP_ACT,            -- Soak Zone 1 Temperature Actual from process parameters
    V_SOAK_ZONE_2_TEMP_ACT,   -- PHF Zone 2 Temperature Actual from process parameters
     V_CGD_ACT_THICKNESS,                   -- Actual Thickness from process parameters
    V_INPUT_WIDTH,                      -- Actual Width from process parameters
    V_RTF_ZONE_4_TEMP_ACT,             -- RTF Zone 4 Temperature Actual from process parameters
    V_RECOILER_TENSION_ACT,            -- Recoiler Tension Actual from process parameters
    V_ENTRY_SPEED_ACT,                 -- Entry Speed Actual from process parameters
    V_POT_TENSION_ACT,                 -- Pot Tension Actual from process parameters
    V_GL_POT_TEMP 

);
  ELSE --n0 3
            H_STM_ID := 'INSERT 2';
           -- commented by jitu on 13/06
     -- IF PART_NUM = 1 THEN     -- IF FIRST PART IS GENERATED
           -- Calculate P_START_TIME and P_END_TIME
        --    SELECT MAX(DATE_TIME)
        --    INTO V_CGD_TS_START_TIME
          --  FROM T_EVENT_LOG
         --   WHERE ID_APP_TAG = 'L1_POR1_SEL' OR ID_APP_TAG = 'L1_POR2_SEL';
     -- ELSE  -- SECOND ONWARDS PARTS
    -- Calculate P_START_TIME and P_END_TIME
           -- SELECT MAX(CGD_TS_END)
          --  INTO V_CGD_TS_START_TIME
           -- FROM T_PDO_INFO
          --  WHERE CGD_ID_COIL_MTHR =MOTHER_COIL ;

    -- END IF;
 ----Here we are fetching starting from T_event_Lof last recoiler signal time            
select DATE_TIME into V_CGD_TS_START_TIME FROM  ( select DATE_TIME, ROW_NUMBER() OVER (ORDER BY DATE_TIME DESC) AS rn from T_event_log where ID_APP_TAG='RECOILER' and VALUE=1)  WHERE rn = 1;
--- End length code-- 

            -- Calculate the duration in minutes and convert to NUMBER
            V_CGD_WORK_DUR := TO_NUMBER(
                EXTRACT(MINUTE FROM (V_CGD_TS_END_TIME - V_CGD_TS_START_TIME)) +
                EXTRACT(HOUR FROM (V_CGD_TS_END_TIME - V_CGD_TS_START_TIME)) * 60 +
                EXTRACT(DAY FROM (V_CGD_TS_END_TIME - V_CGD_TS_START_TIME)) * 24 * 60
            );




            -- FETCHING AVG OF PROCESS PARAMETER BASED ON START END TIME

             SELECT ROUND(NVL(AVG(ENTRY_ACCU_TENSION_ACT),0),2),
                   ROUND( NVL(AVG(ENTRY_SPEED_ACT),0),2),
                   ROUND( NVL(AVG( EXIT_ACCU_TEN_ACT ),0),2),
                   ROUND(NVL(AVG(POR_TENSION_ACT),0),2),
                   ROUND(NVL(AVG(BR2_TENSION_ACT),0),2),
                   ROUND(NVL(AVG(GL_POT_TEMP),0),2),
                   ROUND(NVL(AVG( RECOILER_TENSION_ACT),0),2),
                   ROUND(NVL( AVG(TLL_TENSION_ACT),0),2),
                   ROUND(NVL( AVG(POT_TENSION_ACT),0),2),
                   ROUND(NVL( AVG(PHF_ZONE_1_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(PHF_ZONE_2_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(PHF_ZONE_3_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(RTF_ZONE_1_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(SOAK_ZONE_1_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(SOAK_ZONE_2_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(JCF_ZONE_1_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(JCF_ZONE_2_TEMP_ACT),0),2),
                   ROUND(NVL( AVG( JCF_ZONE_3_TEMP_ACT),0),2),
                   ROUND(NVL( AVG(RTF_ZONE_2_TEMP_ACT),0),2),
                   ROUND(NVL( AVG( RTF_ZONE_3_TEMP_ACT),0),2),
                   ROUND(NVL( AVG( RTF_ZONE_4_TEMP_ACT),0),2),
                  -- ROUND(NVL( MAX( ACT_LENGTH),0),2), -- commented by jitu on 11/06
                   ROUND(NVL( MAX(ACT_WIDTH),0),2),
                   ROUND(NVL( AVG(ACT_WEIGHT),0),2),-- MEASURED WEIGHT-- MEASURED WEIGHT
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
                     V_PHF_ZONE_1_TEMP_ACT ,
                     V_PHF_ZONE_2_TEMP_ACT,
                     V_PHF_ZONE_3_TEMP_ACT,
                     V_RTF_ZONE_1_TEMP_ACT,
                     V_SOAK_ZONE_1_TEMP_ACT ,
                     V_SOAK_ZONE_2_TEMP_ACT,
                     V_JCF_ZONE_1_TEMP_ACT,
                     V_JCF_ZONE_2_TEMP_ACT,
                     V_JCF_ZONE_3_TEMP_ACT ,
                     V_RTF_ZONE_2_TEMP_ACT,
                     V_RTF_ZONE_3_TEMP_ACT,
                     V_RTF_ZONE_4_TEMP_ACT ,
                   --  V_ACT_LENGTH,
                     V_ACT_WIDTH ,
                     V_ACT_WEIGHT,
                     V_CGD_ACT_THICKNESS

       FROM T_PERIODIC_VALUE_LOG WHERE DATE_TIME BETWEEN V_CGD_TS_START_TIME AND V_CGD_TS_END_TIME;

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


    V_NET_WEIGHT := ((V_INPUT_THICKNESS * ( V_ACT_WIDTH +4) * 7.85 *  V_ACT_LENGTH)/1000000);


       V_DAUGHTER_COIL_WEIGHT :=V_NET_WEIGHT ;


              SELECT CGP_MK_CUSTOMER,
                     CGP_SEC1_COIL,
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
    CGD_TAS_FURN_TEN,                -- BR2 Tension Actual
    CGD_TLL_TENSION,                 -- TLL Tension Actual
    CGD_TAS_EN_ACCTEN,               -- Entry Accumulation Tension Actual
    CGD_TAS_POR1_TEN,                -- POR Tension Actual (1)
    CGD_TAS_POR2_TEN,                -- POR Tension Actual (2)
    CGD_TAS_EX_ACCTEN,               -- Exit Accumulation Tension Actual
    CGD_TAS_SPM_ENT_TENSION,         -- Special Tension (Entry, set to 0)
    CGD_TAS_SPM_EXT_TENSION,         -- Special Tension (Exit, set to 0)
    CGD_PHF_ZON1_TMP,                -- PHF Zone 1 Temperature Actual
    CGD_PHF_ZON2_TMP,
    CGD_PHF_ZON3_TMP,                -- PHF Zone 3 Temperature Actual
    CGD_RTF_ZON1_TMP,                -- RTF Zone 1 Temperature Actual
    CGD_RTF_ZON2_TMP,                -- RTF Zone 2 Temperature Actual
    CGD_RTF_ZON3_TMP,                -- RTF Zone 3 Temperature Actual
    CGD_HBR_HEATER_TMP,              -- Heater Temp (set to 0 for now)
    CGD_JCF_HEATER_ZON1_TMP,         -- JCF Zone 1 Temperature Actual
    CGD_JCF_HEATER_ZON2_TMP,         -- JCF Zone 2 Temperature Actual
    CGD_JCF_HEATER_ZON3_TMP,         -- JCF Zone 3 Temperature Actual
    CGD_SOAK_ZON1_TMP,               -- Soak Zone 1 Temperature Actual
    CGD_SOAK_ZON2_TMP,
    CGD_ACT_THICKNESS,                   -- Actual Thickness
      ACT_WIDTH,                      -- Actual Width
    CGD_RTF_ZON4_TMP,                -- RTF Zone 4 Temperature Actual
    RECOILER_TEN_ACT,                -- Recoiler Tension Actual
    PROCESS_SPEED,                   -- Entry Speed Actual
    POT_TEN_ACT,                     -- Pot Tension Actual
    GL_POT_TEMP   


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
   -- V_ACT_WEIGHT,
   V_DAUGHTER_COIL_WEIGHT,            -- theoretical weight calculated based on formula
    V_BR2_TENSION_ACT,                 -- BR2 Tension Actual from process parameters
    V_TLL_TENSION_ACT,                 -- TLL Tension Actual from process parameters
    V_ENTRY_ACCU_TENSION_ACT,          -- Entry Accumulation Tension Actual from process parameters
    V_POR_TENSION_ACT,                 -- POR Tension Actual (1) from process parameters
    V_POR_TENSION_ACT,                 -- POR Tension Actual (2) from process parameters
    V_EXIT_ACCU_TEN_ACT,               -- Exit Accumulation Tension Actual from process parameters
    0,                               -- Special Tension (Entry), set to 0
    0,                               -- Special Tension (Exit), set to 0
    V_PHF_ZONE_1_TEMP_ACT,             -- PHF Zone 1 Temperature Actual from process parameters
    V_PHF_ZONE_2_TEMP_ACT,
    V_PHF_ZONE_3_TEMP_ACT,             -- PHF Zone 3 Temperature Actual from process parameters
    V_RTF_ZONE_1_TEMP_ACT,             -- RTF Zone 1 Temperature Actual from process parameters
    V_RTF_ZONE_2_TEMP_ACT,             -- RTF Zone 2 Temperature Actual from process parameters
    V_RTF_ZONE_3_TEMP_ACT,             -- RTF Zone 3 Temperature Actual from process parameters
    0,                               -- Heater Temp, set to 0 for now
    V_JCF_ZONE_1_TEMP_ACT,             -- JCF Zone 1 Temperature Actual from process parameters
    V_JCF_ZONE_2_TEMP_ACT,             -- JCF Zone 2 Temperature Actual from process parameters
    V_JCF_ZONE_3_TEMP_ACT,             -- JCF Zone 3 Temperature Actual from process parameters
    V_SOAK_ZONE_1_TEMP_ACT,            -- Soak Zone 1 Temperature Actual from process parameters
    V_SOAK_ZONE_2_TEMP_ACT,   -- PHF Zone 2 Temperature Actual from process parameters
     V_CGD_ACT_THICKNESS,                   -- Actual Thickness from process parameters
    V_ACT_WIDTH,                      -- Actual Width from process parameters
    V_RTF_ZONE_4_TEMP_ACT,             -- RTF Zone 4 Temperature Actual from process parameters
    V_RECOILER_TENSION_ACT,            -- Recoiler Tension Actual from process parameters
    V_ENTRY_SPEED_ACT,                 -- Entry Speed Actual from process parameters
    V_POT_TENSION_ACT,                 -- Pot Tension Actual from process parameters
    V_GL_POT_TEMP 

);

        END IF; --n0 3

        --SELECT COIL_ID INTO V_CURR_COIL FROM T_EVENT_TRACKING WHERE ID_APP_TAG_EVENT='RECOILER';
       -- SELECT COIL_ID INTO V_NEXT_COIL FROM T_EVENT_TRACKING WHERE ID_APP_TAG_EVENT='WELD_AT_BR8';
        --IF V_CURR_COIL != V_NEXT_COIL THEN // commented by najmul on 18-06-2025
      --  IF NVL( V_NEXT_COIL, 'NULL') != NVL(V_CURR_COIL, 'NULL') THEN
       -- UPDATE T_EVENT_TRACKING SET COIL_ID=V_NEXT_COIL WHERE ID_APP_TAG_EVENT='RECOILER';

        END IF; --n02
--        



    END IF; --no1
  --  ELSE
         SELECT COIL_ID INTO V_CURR_COIL FROM T_EVENT_TRACKING WHERE ID_APP_TAG_EVENT='RECOILER';
        SELECT COIL_ID INTO V_NEXT_COIL FROM T_EVENT_TRACKING WHERE ID_APP_TAG_EVENT='WELD_AT_BR8';
       --IF V_CURR_COIL != V_NEXT_COIL THEN // commented by najmul on 18-06-2025
        IF NVL( V_NEXT_COIL, 'NULL') != NVL(V_CURR_COIL, 'NULL') THEN
        UPDATE T_EVENT_TRACKING SET COIL_ID=V_NEXT_COIL WHERE ID_APP_TAG_EVENT='RECOILER';
        END IF;

   -- END IF;
  COMMIT;
EXCEPTION
    WHEN OTHERS THEN
        -- Log error details
        DBMS_OUTPUT.PUT_LINE('Error occurred at step: ' || H_STM_ID);
        DBMS_OUTPUT.PUT_LINE('Error message: ' || SQLERRM);

        -- Commit before ending (if necessary)
        COMMIT;
END PDO_GENERATION;
