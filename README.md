CREATE OR REPLACE PROCEDURE P_DELAY_REGISTER IS

P_LINE_SPEED NUMBER(20,4);
TIME_DELAY   TIMESTAMP;
DELAY_STATUS VARCHAR2(3);
DELAY_END    TIMESTAMP;

H_STM_ID     VARCHAR2(200) := NULL;
P_SHIFT      VARCHAR2(5) := 'ERROR';
SPEED_ZERO_NUM NUMBER := 0;
HSD_MESSAGE  VARCHAR2(36) := NULL;

/* Shift Variables */
A_SHIFT TIMESTAMP(6);
B_SHIFT TIMESTAMP(6);
C_SHIFT TIMESTAMP(6);

BEGIN

   H_STM_ID := 'select recent mill_speed';

   BEGIN
      SELECT NVL(MILL_SPEED,0)
      INTO P_LINE_SPEED
      FROM SPML2.T_PERIODIC_VALUE_LOG
      ORDER BY DATE_TIME DESC
      FETCH FIRST 1 ROW ONLY;
   EXCEPTION
      WHEN NO_DATA_FOUND THEN
         P_LINE_SPEED := 0;
   END;

   ----------------------------------------------------------------
   -- IF SPEED < 20 (DELAY START)
   ----------------------------------------------------------------

   IF P_LINE_SPEED < 20 THEN

      H_STM_ID := 'select delay status';

      SELECT REMARKS
      INTO DELAY_STATUS
      FROM SPML2.T_DELAY_SHEET
      WHERE LINE_STOP_TIME IS NOT NULL
      ORDER BY LINE_STOP_TIME DESC
      FETCH FIRST 1 ROW ONLY;

      IF DELAY_STATUS != 'R' THEN

         H_STM_ID := 'get delay time';

         SELECT MIN(DATE_TIME)
         INTO TIME_DELAY
         FROM SPML2.T_PERIODIC_VALUE_LOG
         WHERE MILL_SPEED < 20
         AND DATE_TIME > SYSDATE - INTERVAL '5' MINUTE;

         ----------------------------------------------------------
         -- SHIFT LOGIC FIXED
         ----------------------------------------------------------

         IF TIME_DELAY >= TRUNC(TIME_DELAY) + INTERVAL '6' HOUR
            AND TIME_DELAY < TRUNC(TIME_DELAY) + INTERVAL '14' HOUR THEN

            P_SHIFT := 'A';

         ELSIF TIME_DELAY >= TRUNC(TIME_DELAY) + INTERVAL '14' HOUR
            AND TIME_DELAY < TRUNC(TIME_DELAY) + INTERVAL '22' HOUR THEN

            P_SHIFT := 'B';

         ELSE
            P_SHIFT := 'C';
         END IF;

         ----------------------------------------------------------
         -- SHIFT START TIMES (Midnight Fix for C Shift)
         ----------------------------------------------------------

         A_SHIFT := TRUNC(TIME_DELAY) + INTERVAL '6' HOUR;
         B_SHIFT := TRUNC(TIME_DELAY) + INTERVAL '14' HOUR;
         C_SHIFT := TRUNC(TIME_DELAY) + INTERVAL '22' HOUR;

         -- If between 00:00 and 06:00 → previous day's C shift
         IF TIME_DELAY < TRUNC(TIME_DELAY) + INTERVAL '6' HOUR THEN
            C_SHIFT := TRUNC(TIME_DELAY - 1) + INTERVAL '22' HOUR;
         END IF;

         ----------------------------------------------------------

         HSD_MESSAGE := F_NANNOW_SECOND_TELGRM(TIME_DELAY);

         INSERT INTO SPML2.T_DELAY_SHEET
         (LINE_STOP_TIME, REMARKS, SHIFT, ID_MESSAGE)
         VALUES
         (TIME_DELAY, 'R', P_SHIFT, HSD_MESSAGE);

      END IF;

   END IF;

   ----------------------------------------------------------------
   -- IF SPEED >= 20 (DELAY END)
   ----------------------------------------------------------------

   IF P_LINE_SPEED >= 20 THEN

      H_STM_ID := 'select last running delay';

      SELECT REMARKS, LINE_STOP_TIME
      INTO DELAY_STATUS, TIME_DELAY
      FROM SPML2.T_DELAY_SHEET
      WHERE LINE_STOP_TIME IS NOT NULL
      ORDER BY LINE_STOP_TIME DESC
      FETCH FIRST 1 ROW ONLY;

      IF DELAY_STATUS = 'R' THEN

         H_STM_ID := 'get delay end time';

         SELECT MIN(DATE_TIME)
         INTO DELAY_END
         FROM SPML2.T_PERIODIC_VALUE_LOG
         WHERE MILL_SPEED != 0
         AND DATE_TIME > SYSDATE - INTERVAL '5' MINUTE;

         H_STM_ID := 'update delay end';

         UPDATE SPML2.T_DELAY_SHEET
         SET LINE_START_TIME = DELAY_END,
             DELAY_DURATION  = TIME_DIFF_MINTE(TIME_DELAY, DELAY_END),
             REMARKS         = 'E',
             TOM             = SYSDATE
         WHERE REMARKS = 'R';

      END IF;

   END IF;

   DBMS_OUTPUT.PUT_LINE(SPEED_ZERO_NUM);
   DBMS_OUTPUT.PUT_LINE(DELAY_END);

EXCEPTION
   WHEN OTHERS THEN
      HANDLE_ERROR('SPM','P_DELAY_REGISTER',H_STM_ID,SQLCODE,
                   SUBSTR(SQLERRM(SQLCODE),1,100),
                   NULL,'T_DELAY_SHEET');
      COMMIT;

END P_DELAY_REGISTER;