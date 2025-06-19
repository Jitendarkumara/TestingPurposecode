create or replace PROCEDURE PDO_GENERATION AS
    MOTHER_COIL VARCHAR2(50);
    DAUGHTER_COIL VARCHAR2(50);
    PART_NUM INT := 0;
   

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
                   ROUND(NVL(AVG(POR_TENSION_ACT),0),2)
               
                   INTO
                     V_ENTRY_ACCU_TENSION_ACT,
                  --   V_ENTRY_SPEED_ACT,
                     V_EXIT_ACCU_TEN_ACT,
                     V_POR_TENSION_ACT,
                     
-- commented by jitu on 10/06/2025  
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

           INSERT INTO T_PDO_INFO (
    CGD_ID_COIL,                     -- Coil ID
    CGD_MK_CUSTOMER,                 -- Customer name
    CGD_TS_START,                    -- Start time
   


)
VALUES (
     MOTHER_COIL,                     -- Coil ID from the parent coil
    V_CUSTOMER_NAME,                   -- Customer name from PDI
    V_CGD_TS_START_TIME,                    -- Start time from the event log
 
);
  ELSE --n0 3
            H_STM_ID := 'INSERT 2';
         
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
                   ROUND( NVL(AVG( EXIT_ACCU_TEN_ACT ),0),2)
                
                   INTO
                     V_ENTRY_ACCU_TENSION_ACT,
                     V_ENTRY_SPEED_ACT,
                     V_EXIT_ACCU_TEN_ACT,
                    

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
    

)
VALUES (
     DAUGHTER_COIL,                     -- Coil ID from the parent coil
    V_CUSTOMER_NAME,                   -- Customer name from PDI
    V_CGD_TS_START_TIME,                    -- Start time from the event log
   
);

        END IF; --n0 3


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
