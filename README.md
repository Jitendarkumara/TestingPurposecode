IF P_START_TIME_VAR IS NULL THEN
    -- If not found, use the existing logic to calculate P_START_TIME
    SELECT MAX(DATE_TIME)
    INTO P_START_TIME
    FROM T_EVENT_LOG
    WHERE ID_APP_TAG IN ('UNC_1_SELECTED', 'UNC_2_SELECTED');

    -- Check if the calculated time is older than 60 minutes
    IF (SYSDATE - P_START_TIME) * 1440 > 60 THEN  -- 1440 minutes per day
        P_START_TIME := SYSDATE;  -- or use another fallback if needed
    END IF;
ELSE
    P_START_TIME := P_START_TIME_VAR;
END IF;