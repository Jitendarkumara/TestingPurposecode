CREATE OR REPLACE TRIGGER TR_GP1_SETUP_CALCULATION_NEW
AFTER INSERT ON T_GP1_PDI
FOR EACH ROW
BEGIN
    BEGIN
        P_LINE_TEN_CALC_DATA_MODIFIED(
            :NEW.CGP_ID_COIL,
            :NEW.CGP_SEC1_COIL,
            :NEW.CGP_SEC2_COIL,
            :NEW.CGP_TDC_NO
        );

    EXCEPTION
        WHEN OTHERS THEN
            -- IMPORTANT: Do NOT call logging procedure if it commits
            DBMS_OUTPUT.PUT_LINE(
                'Trigger Error: ' || SQLERRM
            );
    END;
END;
/