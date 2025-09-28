CREATE OR REPLACE PROCEDURE p_production_report (
    p_coil_id IN  VARCHAR2,
    issucess  OUT VARCHAR2
)
AS
BEGIN
    -- Insert the latest record for the given coil
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
        PRODUCT_CODE
    FROM T_PL_PDO_LEVEL2
    WHERE HR_COIL_NO = p_coil_id
    ORDER BY END_TIME DESC
    FETCH FIRST 1 ROW ONLY;

    issucess := 'S';
    COMMIT;
    DBMS_OUTPUT.put_line('Record inserted into DBPROD.T_RM_PKL_PDO');

EXCEPTION
    WHEN OTHERS THEN
        -- If the insert fails, attempt to update the source table
        DBMS_OUTPUT.put_line('Insert failed: ' || SQLERRM);

        BEGIN
            UPDATE T_PL_PDO_LEVEL2
            SET flag_id = 170
            WHERE CR_COIL_ID = p_coil_id;

            DBMS_OUTPUT.put_line('flag_id updated to 170 for coil ' || p_coil_id);
        EXCEPTION
            WHEN OTHERS THEN
                DBMS_OUTPUT.put_line('Update also failed: ' || SQLERRM);
        END;

        issucess := 'F';
        COMMIT;
END p_production_report;
/