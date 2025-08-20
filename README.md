CREATE OR REPLACE PROCEDURE TRANSFER_TO_MES_DELAY(
  p_Delay_Id IN NUMBER
)
AUTHID DEFINER   -- ✅ Explicitly state it, removes warning
IS
  id_message VARCHAR2(30);
  READVALUE VARCHAR2(1);
  V_AGENCY_CODE VARCHAR2(100);
  V_AGENCY_CODE_VALUE VARCHAR2(150);
BEGIN
  -- Generate a unique message ID
  SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD-HH24.MI.SS') || '.' ||
         LPAD(ABS(MOD(DBMS_RANDOM.RANDOM, 1000000)), 6, '0')
  INTO id_message
  FROM DUAL;

  -- Retrieve the L3_READ_FLAG value for the given delay ID
  SELECT L3_READ_FLAG, AGENCY_CODE
  INTO READVALUE, V_AGENCY_CODE
  FROM T_DELAY_SHEET_TEM
  WHERE DELAY_ID = p_Delay_Id;

  SELECT TDR_LG_CD || '-' || TDR_AGENCY_DESC
  INTO V_AGENCY_CODE_VALUE
  FROM T_AGENCY_DTL
  WHERE TDR_AGENCY_DESC = V_AGENCY_CODE;

  -- Check if L3_READ_FLAG is 'N' before inserting
  IF READVALUE = 'N' THEN

    -- Insert data into the target table
    INSERT INTO DBPROD.T_L2_LINE_DELAY_INT@DBL_GP2K_L2L3_DL (
            LLD_ID_MESSAGE,
            LLD_TM_LINE_STOP,
            LLD_TM_LINE_START,
            LLD_DELAY_AGENT,
            LLD_DELAY_CODE,
            LLD_REMARKS,
            LLD_PLANT_CODE,
            LLD_STAGE_CODE,
            LLD_READ_FLAG
    )
    SELECT
      id_message,
      LINE_STOP_TIME,
      LINE_START_TIME,
      V_AGENCY_CODE_VALUE,
      REASON_CODE,
      REMARKS,
      '0784',
      'GP',
      'N'
    FROM T_DELAY_SHEET_TEM
    WHERE DELAY_ID = p_Delay_Id;

    -- Mark the record as processed
    UPDATE T_DELAY_SHEET_TEM
    SET L3_READ_FLAG = 'Y'
    WHERE DELAY_ID = p_Delay_Id;

    COMMIT;
  END IF;

EXCEPTION
  WHEN OTHERS THEN
    ROLLBACK;
    RAISE;
END TRANSFER_TO_MES_DELAY;