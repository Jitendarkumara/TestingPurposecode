DECLARE
    V_SUCCESS VARCHAR2(20);
BEGIN
    P_PROCESS_DATA_INSERT(
        'KP203',                   -- P_COIL_ID
        '20250805124426',          -- MIN_RUNID
        '20250805124456',          -- MAX_RUNID
        V_SUCCESS                  -- ISUCCESS OUT
    );

    DBMS_OUTPUT.PUT_LINE('Success = ' || V_SUCCESS);
END;