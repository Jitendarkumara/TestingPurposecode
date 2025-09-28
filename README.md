create or replace PROCEDURE       p_production_report (
    p_coil_id IN VARCHAR2,
    issucess  OUT VARCHAR2
) AS

    p_mcoil_id   VARCHAR2(20) := NULL;
    p_start_time TIMESTAMP := sysdate;
    p_end_time   TIMESTAMP := sysdate;
    p_width      NUMBER;
    p_thickness  NUMBER;
    p_length     NUMBER;
    p_weight     NUMBER;
    p_msg_id     VARCHAR2(100) := f_nannow_second_telgrm(sysdate);
    p_shift      VARCHAR2(10) := 0;
    p_time_diff  NUMBER := 0;
    h_stm_id     VARCHAR2(50) := NULL;
    p_target_thk NUMBER := 0;

    BEGIN
        INSERT INTO
       
       DBPROD.T_RM_PKL_PDO@WCRMDEVL2
        (
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
            ( SELECT
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
                
                FROM 
                T_PL_PDO_LEVEL2

                WHERE

                   HR_COIL_NO = p_coil_id
                ORDER BY
                    --prd.daughter_end_time DESC
                    END_TIME DESC
                FETCH FIRST 1 ROW ONLY
            );

        issucess := 'S';
        COMMIT;
        dbms_output.put_line('INSERTED');
    EXCEPTION
        WHEN OTHERS THEN
 

    h_stm_id := 'UPDATE 1';
    BEGIN
        UPDATE T_PL_PDO_LEVEL2
        SET
            flag_id = 170
        WHERE
            CR_COIL_ID = p_coil_id;

    EXCEPTION
        WHEN OTHERS THEN
 

    END;
    COMMIT;
END p_production_report;
