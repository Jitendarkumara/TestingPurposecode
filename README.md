  INSERT INTO DBPROD.T_RM_PKL_PDO@WCRMDEVL2 (
        CR_COIL_ID,
        START_TIME,
        END_TIME,
        SHIFT,
        TIME_PROCESSED,
        GRADE,
        NEXT_PROCESS,
        HR_COIL_NO,
        INPUT_THK,
        INPUT_WIDTH,
        INPUT_COIL_WT,
        LENGTH,
        CR_TDC,
        CR_Q_CODE,
        SALES_ORDER,
        SO_ITEM,
        PRODUCT_CODE
    )
    SELECT
        CR_COIL_ID,
        START_TIME,
        END_TIME,
        SHIFT,
        TIME_PROCESSED,
        GRADE,
        NEXT_PROCESS,
        HR_COIL_NO,
        INPUT_THK,
        INPUT_WIDTH,
        INPUT_COIL_WT,
        LENGTH,
        CR_TDC,
        CR_Q_CODE,
        SALES_ORDER,
        SO_ITEM,
        PRODUCT_CODE    -- bind PL/SQL variable here
    FROM (
        SELECT t.*,
               ROW_NUMBER() OVER (PARTITION BY CR_COIL_ID ORDER BY END_TIME DESC) AS rn
        FROM   T_PL_PDO_LEVEL2 t
        WHERE  CR_COIL_ID = 'K4B0631600'     -- use the parameter, not a hardcoded value
    )
    WHERE rn = 1;
