create or replace TRIGGER TR_GP1_SETUP_CALCULATION_NEW
AFTER INSERT ON T_GP1_PDI
FOR EACH ROW
DECLARE
    v_sec1_coil NUMBER;
    h_stm_id    VARCHAR2(30);
BEGIN
    v_sec1_coil := :NEW.CGP_SEC1_COIL;

    DBMS_OUTPUT.PUT_LINE(
        'Trigger values => ID:' || :NEW.CGP_ID_COIL ||
        ' THK:' || v_sec1_coil ||
        ' WIDTH:' || :NEW.CGP_SEC2_COIL ||
        ' TDC:' || :NEW.CGP_TDC_NO
    );

--    h_stm_id := 'PROC_CALL_1';
--    P_LINE_TEN_CALC_DATA(
--        :NEW.CGP_ID_COIL,
--        v_sec1_coil,
--        :NEW.CGP_SEC2_COIL
--    );

    h_stm_id := 'PROC_CALL_2';
    P_LINE_TEN_CALC_DATA_MODIFIED(
        :NEW.CGP_ID_COIL,
        v_sec1_coil,
        :NEW.CGP_SEC2_COIL,
        :NEW.CGP_TDC_NO
    );

EXCEPTION
    WHEN OTHERS THEN
        HANDLE_ERROR_NO_COMMIT(
            'GP1 L2',
            'TR_GP1_SETUP_CALCULATION_NEW',
            h_stm_id,
            SQLCODE,
            SUBSTR(SQLERRM, 1, 300),
            NULL,
            'T_GP1_PDI'
        );
END;
