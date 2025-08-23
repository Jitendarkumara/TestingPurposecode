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
     -- variabbles for auto GR management 31 July 25  
v_INPUT_WEIGHT_MB NUMBER(8,2):=0; -- mother batch input weight
v_DGTR_BATCH_COMBINED_WGT NUMBER(8,2):=0;
v_MAX_ALLOWED_WGT NUMBER(8,2):=0;

-- -----------------------------------------

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
-- added 06-june 25
SELECT nvl(CGP_MS_COIL,0) INTO v_INPUT_WEIGHT_MB FROM T_GP1_PDI WHERE CGP_ID_COIL=MOTHER_COIL; 
 v_MAX_ALLOWED_WGT:=v_INPUT_WEIGHT_MB * 1.10;
 
 
 -- ------------checking here mother coil id  is available or not ---------------------
        IF DAUGHTER_COIL IS NULL THEN ---no. 3
        DAUGHTER_COIL:=MOTHER_COIL;
        END IF; --n02
  
 ----Here we are fetching starting from T_event_Lof last recoiler signal time            
select DATE_TIME into V_CGD_TS_START_TIME FROM  ( select DATE_TIME, ROW_NUMBER() OVER (ORDER BY DATE_TIME DESC) AS rn from T_event_log where ID_APP_TAG='RECOILER' and VALUE=1)  WHERE rn = 1;
--- End length code-- 


 SELECT nvl(sum(CGD_MS_ACTL),0) into v_DGTR_BATCH_COMBINED_WGT FROM T_PDO_INFO  where CGD_ID_COIL_MTHR=MOTHER_COIL;
--here if weight will be Null then return 0

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
                     CGP_MS_COIL,CGP_SEC2_ORD --Jitu

                     INTO
                     V_CUSTOMER_NAME,
                     V_INPUT_THICKNESS,
                     V_INPUT_WIDTH,
                     V_INPUT_WEIGHT,V_ACT_WIDTH

                     FROM T_GP1_PDI WHERE CGP_ID_COIL=MOTHER_COIL;
                     --Jitu is calcalting here weight of the daughter coil


     
 V_NET_WEIGHT := ((V_INPUT_THICKNESS * ( V_INPUT_WIDTH +4) * 7.85 *  V_ACT_LENGTH)/1000000);

       V_DAUGHTER_COIL_WEIGHT :=V_NET_WEIGHT ;
v_DGTR_BATCH_COMBINED_WGT:=V_DAUGHTER_COIL_WEIGHT+v_DGTR_BATCH_COMBINED_WGT;

             
IF(v_DGTR_BATCH_COMBINED_WGT<=v_MAX_ALLOWED_WGT) THEN
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
                CGD_MS_ACTL,
                CGD_ACT_THICKNESS,
                ACT_WIDTH,
                PROCESS_SPEED
            ) VALUES (
                DAUGHTER_COIL,
                V_CUSTOMER_NAME,
                V_CGD_TS_START_TIME,
                V_CGD_TS_END_TIME,
                V_CGD_WORK_DUR,
                MOTHER_COIL,
                V_INPUT_THICKNESS,
                V_INPUT_WIDTH,
                V_ACT_LENGTH,
                --V_ACT_WEIGHT,
                V_NET_WEIGHT,
               -- V_CGD_ACT_THICKNESS,
                 V_INPUT_THICKNESS, --Added by jitu on 07/08/2025 
                V_ACT_WIDTH,
                V_ENTRY_SPEED_ACT
            );
end if;

   END IF; --n0 3

     
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
