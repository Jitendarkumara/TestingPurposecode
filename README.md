IF (@CURRENT_LENGTH BETWEEN (@PDI_LENGTH/4 - 5) AND (@PDI_LENGTH/4 + 5))
    AND NOT EXISTS (SELECT 1 FROM FINAL_COIL_DATA WHERE Coil_ID=@Coil_ID AND Q1_FLAG=1)
    BEGIN
        UPDATE FINAL_COIL_DATA
        SET 
            Q1min = (SELECT MIN(L1_SPM_ELG_ACT) FROM pvl_table WHERE Coil_ID=@Coil_ID AND Current_Length <= @CURRENT_LENGTH),
            Q1max = (SELECT MAX(L1_SPM_ELG_ACT FROM pvl_table_LOG WHERE Coil_ID=@Coil_ID AND Current_Length <= @CURRENT_LENGTH),
            Q1avg = (SELECT AVG(L1_SPM_ELG_ACT) FROM pvl_table_LOG WHERE Coil_ID=@Coil_ID AND Current_Length <= @CURRENT_LENGTH),
            Q1_FLAG = 1
        WHERE Coil_ID=@Coil_ID
    END

    ------------------ Q2 ------------------
    IF (@CURRENT_LENGTH BETWEEN (@PDI_LENGTH/2 - 5) AND (@PDI_LENGTH/2 + 5))
    AND NOT EXISTS (SELECT 1 FROM FINAL_COIL_DATA WHERE Coil_ID=@Coil_ID AND Q2_FLAG=1)
    BEGIN
        UPDATE FINAL_COIL_DATA
        SET 
            Q2min = (SELECT MIN(L1_SPM_ELG_ACT) FROM ELONGATION_LOG WHERE Coil_ID=@Coil_ID AND Current_Length <= @CURRENT_LENGTH),
            Q2max = (SELECT MAX(L1_SPM_ELG_ACT) FROM ELONGATION_LOG WHERE Coil_ID=@Coil_ID AND Current_Length <= @CURRENT_LENGTH),
            Q2avg = (SELECT AVG(L1_SPM_ELG_ACT) FROM ELONGATION_LOG WHERE Coil_ID=@Coil_ID AND Current_Length <= @CURRENT_LENGTH),
            Q2_FLAG = 1
        WHERE Coil_ID=@Coil_ID
    END

    ------------------ Q3 ------------------
    IF (@CURRENT_LENGTH BETWEEN ((@PDI_LENGTH*3)/4 - 5) AND ((@PDI_LENGTH*3)/4 + 5))
    AND NOT EXISTS (SELECT 1 FROM FINAL_COIL_DATA WHERE Coil_ID=@Coil_ID AND Q3_FLAG=1)
    BEGIN
        UPDATE FINAL_COIL_DATA
        SET 
            Q3min = (SELECT MIN(L1_SPM_ELG_ACT) FROM ELONGATION_LOG WHERE Coil_ID=@Coil_ID AND Current_Length <= @CURRENT_LENGTH),
            Q3max = (SELECT MAX(L1_SPM_ELG_ACT) FROM ELONGATION_LOG WHERE Coil_ID=@Coil_ID AND Current_Length <= @CURRENT_LENGTH),
            Q3avg = (SELECT AVG(L1_SPM_ELG_ACT) FROM ELONGATION_LOG WHERE Coil_ID=@Coil_ID AND Current_Length <= @CURRENT_LENGTH),
            Q3_FLAG = 1
        WHERE Coil_ID=@Coil_ID
    END

    ------------------ Q4 ------------------
    IF (@CURRENT_LENGTH BETWEEN (@PDI_LENGTH - 5) AND (@PDI_LENGTH + 5))
    AND NOT EXISTS (SELECT 1 FROM FINAL_COIL_DATA WHERE Coil_ID=@Coil_ID AND Q4_FLAG=1)
    BEGIN
        UPDATE FINAL_COIL_DATA
        SET 
            Q4min = (SELECT MIN(L1_SPM_ELG_ACT) FROM ELONGATION_LOG WHERE Coil_ID=@Coil_ID),
            Q4max = (SELECT MAX(L1_SPM_ELG_ACT) FROM ELONGATION_LOG WHERE Coil_ID=@Coil_ID),
            Q4avg = (SELECT AVG(L1_SPM_ELG_ACT) FROM ELONGATION_LOG WHERE Coil_ID=@Coil_ID),
            Q4_FLAG = 1,
            End_Time = @END_TIME
        WHERE Coil_ID=@Coil_ID
    END