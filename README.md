  SELECT
        CR_COIL_ID,
        START_TIME,
        max(END_TIME),
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
    FROM T_PL_PDO_LEVEL2
    WHERE CR_COIL_ID = 'K4B0631600'  ORDER BY END_TIME DESC
    FETCH FIRST 1 ROW ONLY ;
