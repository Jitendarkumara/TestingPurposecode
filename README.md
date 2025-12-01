CREATE OR REPLACE PROCEDURE P_DELAY_REGISTER IS
  P_LINE_SPEED NUMBER := NULL;

  TIME_DELAY TIMESTAMP;
  DELAY_STATUS VARCHAR2(3);
  DELAY_END TIMESTAMP;

  H_STM_ID VARCHAR2(200) := NULL;
  P_SHIFT VARCHAR2(5) := 'ERROR';

  SPEED_ZERO_NUM NUMBER := 0;
  HSD_MESSAGE VARCHAR2(36) := NULL;

  A_SHIFT TIMESTAMP(6) := TRUNC(SYSDATE) + 6/24;
  B_SHIFT TIMESTAMP(6) := TRUNC(SYSDATE) + 14/24;
  C_SHIFT TIMESTAMP(6) := TRUNC(SYSDATE) + 22/24;
  MID_NIGHT TIMESTAMP(6) := TRUNC(SYSDATE+1);

  -- local helper vars
  v_count NUMBER := 0;
BEGIN
  -- get latest speed safely (avoid NO_DATA_FOUND)
  H_STM_ID := 'select latest speed';
  BEGIN
    SELECT ENTRY_SPEED_ACT
      INTO P_LINE_SPEED
      FROM (
        SELECT ENTRY_SPEED_ACT
        FROM T_PERIODIC_VALUE_LOG
        ORDER BY RUN_ID DESC
      )
    WHERE ROWNUM = 1;
  EXCEPTION
    WHEN NO_DATA_FOUND THEN
      P_LINE_SPEED := NULL; -- or 0 if you prefer
    WHEN OTHERS THEN
      -- log and re-raise after handling
      RAISE;
  END;

  IF P_LINE_SPEED IS NULL THEN
    -- nothing to do if no reading
    RETURN;
  END IF;

  IF P_LINE_SPEED < 20 THEN
    H_STM_ID := 'select last remark';
    -- get latest REMARKS (safe)
    BEGIN
      SELECT REMARKS
        INTO DELAY_STATUS
        FROM (
          SELECT REMARKS
          FROM T_DELAY_SHEET_TEM
          WHERE LINE_STOP_TIME IS NOT NULL
          ORDER BY LINE_STOP_TIME DESC
        )
      WHERE ROWNUM = 1;
    EXCEPTION
      WHEN NO_DATA_FOUND THEN
        DELAY_STATUS := NULL;
      WHEN OTHERS THEN
        RAISE;
    END;

    IF DELAY_STATUS IS NULL OR DELAY_STATUS <> 'R' THEN
      H_STM_ID := 'check time_delay';
      BEGIN
        SELECT MIN(DATE_TIME)
          INTO TIME_DELAY
          FROM T_PERIODIC_VALUE_LOG
         WHERE ENTRY_SPEED_ACT < 20
           AND (DATE_TIME > SYSDATE - INTERVAL '5' MINUTE);
      EXCEPTION
        WHEN NO_DATA_FOUND THEN
          TIME_DELAY := NULL;
      END;

      IF TIME_DELAY IS NOT NULL THEN
        H_STM_ID := 'determine shift';
        SELECT CASE
                 WHEN TO_CHAR(TIME_DELAY,'HH24:MI') BETWEEN '06:00' AND '13:59' THEN 'A'
                 WHEN TO_CHAR(TIME_DELAY,'HH24:MI') BETWEEN '14:00' AND '21:59' THEN 'B'
                 ELSE 'C'
               END
          INTO P_SHIFT
          FROM DUAL;

        H_STM_ID := 'Insert 1';
        HSD_MESSAGE := F_NANNOW_SECOND_TELGRM(TIME_DELAY);

        INSERT INTO T_DELAY_SHEET_TEM (LINE_STOP_TIME, REMARKS, SHIFT, ID_MSG)
        VALUES (TIME_DELAY, 'R', P_SHIFT, HSD_MESSAGE);
      END IF;
    END IF;

    -- If last remark was 'R', we try to close or split by shift boundaries
    IF DELAY_STATUS = 'R' THEN
      H_STM_ID := 'get latest stop time';
      BEGIN
        SELECT LINE_STOP_TIME INTO TIME_DELAY
          FROM (
            SELECT LINE_STOP_TIME
            FROM T_DELAY_SHEET_TEM
            WHERE LINE_STOP_TIME IS NOT NULL
            ORDER BY LINE_STOP_TIME DESC
          )
        WHERE ROWNUM = 1;
      EXCEPTION
        WHEN NO_DATA_FOUND THEN
          TIME_DELAY := NULL;
      END;

      IF TIME_DELAY IS NOT NULL THEN
        -- A shift boundary
        H_STM_ID := 'SELECT A SHIFT';
        IF (TIME_DELAY < A_SHIFT AND A_SHIFT < SYSDATE) THEN
          UPDATE T_DELAY_SHEET_TEM
             SET LINE_START_TIME = A_SHIFT,
                 DELAY_DURATION  = TIME_DIFF_MINTE(TIME_DELAY, A_SHIFT),
                 REMARKS        = 'E',
                 TOM            = SYSDATE
           WHERE REMARKS = 'R';

          INSERT INTO T_DELAY_SHEET_TEM (LINE_STOP_TIME, REMARKS, SHIFT, ID_MSG)
          VALUES (A_SHIFT, 'R', 'A', F_NANNOW_SECOND_TELGRM(A_SHIFT));
        END IF;

        H_STM_ID := 'SELECT B SHIFT';
        IF (TIME_DELAY < B_SHIFT AND B_SHIFT < SYSDATE) THEN
          UPDATE T_DELAY_SHEET_TEM
             SET LINE_START_TIME = B_SHIFT,
                 DELAY_DURATION  = TIME_DIFF_MINTE(TIME_DELAY, B_SHIFT),
                 REMARKS        = 'E',
                 TOM            = SYSDATE
           WHERE REMARKS = 'R';

          INSERT INTO T_DELAY_SHEET_TEM (LINE_STOP_TIME, REMARKS, SHIFT, ID_MSG)
          VALUES (B_SHIFT, 'R', 'B', F_NANNOW_SECOND_TELGRM(B_SHIFT));
        END IF;

        H_STM_ID := 'SELECT C SHIFT';
        IF (TIME_DELAY < C_SHIFT AND C_SHIFT < SYSDATE) THEN
          UPDATE T_DELAY_SHEET_TEM
             SET LINE_START_TIME = C_SHIFT,
                 DELAY_DURATION  = TIME_DIFF_MINTE(TIME_DELAY, C_SHIFT),
                 REMARKS        = 'E',
                 TOM            = SYSDATE
           WHERE REMARKS = 'R';

          -- NOTE: fixed to use C_SHIFT instead of B_SHIFT
          INSERT INTO T_DELAY_SHEET_TEM (LINE_STOP_TIME, REMARKS, SHIFT, ID_MSG)
          VALUES (C_SHIFT, 'R', 'C', F_NANNOW_SECOND_TELGRM(C_SHIFT));
        END IF;
      END IF;
    END IF;

  ELSE -- P_LINE_SPEED >= 20
    H_STM_ID := 'SELECT 4';
    H_STM_ID := 'select latest delay row';
    BEGIN
      SELECT REMARKS, LINE_STOP_TIME
        INTO DELAY_STATUS, TIME_DELAY
        FROM (
          SELECT REMARKS, LINE_STOP_TIME
          FROM T_DELAY_SHEET_TEM
          WHERE LINE_STOP_TIME IS NOT NULL
          ORDER BY LINE_STOP_TIME DESC
        )
      WHERE ROWNUM = 1;
    EXCEPTION
      WHEN NO_DATA_FOUND THEN
        DELAY_STATUS := NULL;
        TIME_DELAY := NULL;
      WHEN OTHERS THEN
        RAISE;
    END;

    IF DELAY_STATUS = 'R' THEN
      H_STM_ID := 'SELECT 5';
      BEGIN
        SELECT MIN(DATE_TIME)
          INTO DELAY_END
          FROM T_PERIODIC_VALUE_LOG
         WHERE ENTRY_SPEED_ACT != 0
           AND (DATE_TIME > SYSDATE - INTERVAL '5' MINUTE);
      EXCEPTION
        WHEN NO_DATA_FOUND THEN
          DELAY_END := NULL;
      END;

      IF DELAY_END IS NOT NULL THEN
        H_STM_ID := 'UPDATE 1';
        UPDATE T_DELAY_SHEET_TEM
           SET LINE_START_TIME = DELAY_END,
               DELAY_DURATION  = TIME_DIFF_MINTE(TIME_DELAY, DELAY_END),
               REMARKS        = 'E',
               TOM            = SYSDATE
         WHERE REMARKS = 'R';
      END IF;
    END IF;
  END IF;

  dbms_output.put_line('SPEED_ZERO_NUM = ' || NVL(TO_CHAR(SPEED_ZERO_NUM), 'NULL'));
  dbms_output.put_line('DELAY_END = ' || NVL(TO_CHAR(DELAY_END), 'NULL'));

  COMMIT; -- commit successful path

EXCEPTION
  WHEN NO_DATA_FOUND THEN
    -- unexpected, but log and call handler
    BEGIN
      HANDL_EERROR('CCL LEVEL 2', 'P_DELAY_REGISTER', H_STM_ID, SQLCODE,
                   SUBSTR(SQLERRM, 1, 100), NULL, 'T_DELAY_SHEET_TEM');
    EXCEPTION
      WHEN OTHERS THEN
        NULL; -- ensure HANDL_EERROR does not raise to caller
    END;
    ROLLBACK;
  WHEN OTHERS THEN
    -- log error, do not let handler raise again
    BEGIN
      HANDL_EERROR('CCL LEVEL 2', 'P_DELAY_REGISTER', H_STM_ID, SQLCODE,
                   SUBSTR(SQLERRM, 1, 100), NULL, 'T_DELAY_SHEET_TEM');
    EXCEPTION
      WHEN OTHERS THEN
        NULL;
    END;
    ROLLBACK;
    -- re-raise if you want caller to see it: RAISE;
END P_DELAY_REGISTER;
/