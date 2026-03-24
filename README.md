CREATE OR REPLACE PROCEDURE p_elongation_data
IS
    STARTTIME       TIMESTAMP;
    ENDTIME         TIMESTAMP := SYSTIMESTAMP;

    p_CoilID        VARCHAR2(50);
    p_POR_DIA       NUMBER;
    p_DTR_DIA       NUMBER;
    p_THICK         NUMBER;
    p_WIDTH         NUMBER;
    p_WEIGHT        NUMBER;

    PDI_LENGTH      NUMBER;
    CURRENT_LENGTH  NUMBER;

    v_q1_cnt NUMBER;
    v_q2_cnt NUMBER;
    v_q3_cnt NUMBER;
    v_q4_cnt NUMBER;

BEGIN

-------------------------------------------------------
-- 1. Get Coil ID from POR location
-------------------------------------------------------

SELECT COIL_ID
INTO p_CoilID
FROM T_SPM_COIL_PROCESSING
WHERE LOCATION = 'POR'
FETCH FIRST 1 ROW ONLY;

-------------------------------------------------------
-- 2. Last END_TIME from T_ELONGATION_TEMP
-------------------------------------------------------

SELECT MAX(END_TIME)
INTO STARTTIME
FROM T_ELONGATION_TEMP;

-------------------------------------------------------
-- 3. POR_DIA (Min) & DTR_DIA (Max)
-------------------------------------------------------

SELECT ROUND(MIN(POR_DIA)), ROUND(MAX(DTR_DIA))
INTO p_POR_DIA, p_DTR_DIA
FROM SPM12.T_PERIODIC_VALUE_LOG
WHERE DATE_TIME BETWEEN STARTTIME AND ENDTIME;

-------------------------------------------------------
-- 4. Get PDI details
-------------------------------------------------------

SELECT TARGET_THK, COIL_LENGTH
INTO p_THICK, PDI_LENGTH
FROM SPM12.T_PDI_SPM_LEVEL2
WHERE COIL_ID = p_CoilID
FETCH FIRST 1 ROW ONLY;

-------------------------------------------------------
-- 5. Compute Current Length from Diameter
-------------------------------------------------------

CURRENT_LENGTH :=
ROUND(((0.78 * (p_DTR_DIA * p_DTR_DIA - 508 * 508)) / p_THICK) / 1000);

-------------------------------------------------------
-- Get flag counts
-------------------------------------------------------

SELECT COUNT(*) INTO v_q1_cnt FROM T_ELONGATION_TEMP WHERE Q1_FLAG = 1;
SELECT COUNT(*) INTO v_q2_cnt FROM T_ELONGATION_TEMP WHERE Q2_FLAG = 1;
SELECT COUNT(*) INTO v_q3_cnt FROM T_ELONGATION_TEMP WHERE Q3_FLAG = 1;
SELECT COUNT(*) INTO v_q4_cnt FROM T_ELONGATION_TEMP WHERE Q4_FLAG = 1;

-------------------------------------------------------
-- Q1 Check
-------------------------------------------------------

IF (CURRENT_LENGTH BETWEEN (PDI_LENGTH/4 - 5) AND (PDI_LENGTH/4 + 5))
AND v_q1_cnt = 0
THEN

UPDATE T_ELONGATION_TEMP
SET
Q1min = (SELECT MIN(L1_SPM_ELG_ACT) FROM T_PERIODIC_VALUE_LOG),
Q1max = (SELECT MAX(L1_SPM_ELG_ACT) FROM T_PERIODIC_VALUE_LOG),
Q1avg = (SELECT AVG(L1_SPM_ELG_ACT) FROM T_PERIODIC_VALUE_LOG),
END_TIME = SYSTIMESTAMP,
Q1_FLAG = 1;

END IF;

-------------------------------------------------------
-- Q2 Check
-------------------------------------------------------

IF (CURRENT_LENGTH BETWEEN (PDI_LENGTH/2 - 5) AND (PDI_LENGTH/2 + 5))
AND v_q2_cnt = 0
THEN

UPDATE T_ELONGATION_TEMP
SET
Q2min = (SELECT MIN(L1_SPM_ELG_ACT) FROM T_PERIODIC_VALUE_LOG),
Q2max = (SELECT MAX(L1_SPM_ELG_ACT) FROM T_PERIODIC_VALUE_LOG),
Q2avg = (SELECT AVG(L1_SPM_ELG_ACT) FROM T_PERIODIC_VALUE_LOG),
END_TIME = SYSTIMESTAMP,
Q2_FLAG = 1;

END IF;

-------------------------------------------------------
-- Q3 Check
-------------------------------------------------------

IF (CURRENT_LENGTH BETWEEN ((PDI_LENGTH*3)/4 - 5) AND ((PDI_LENGTH*3)/4 + 5))
AND v_q3_cnt = 0
THEN

UPDATE T_ELONGATION_TEMP
SET
Q3min = (SELECT MIN(L1_SPM_ELG_ACT) FROM T_PERIODIC_VALUE_LOG),
Q3max = (SELECT MAX(L1_SPM_ELG_ACT) FROM T_PERIODIC_VALUE_LOG),
Q3avg = (SELECT AVG(L1_SPM_ELG_ACT) FROM T_PERIODIC_VALUE_LOG),
END_TIME = SYSTIMESTAMP,
Q3_FLAG = 1;

END IF;

-------------------------------------------------------
-- Q4 Check
-------------------------------------------------------

IF (CURRENT_LENGTH BETWEEN (PDI_LENGTH - 5) AND (PDI_LENGTH + 5))
AND v_q4_cnt = 0
THEN

UPDATE T_ELONGATION_TEMP
SET
Q4min = (SELECT MIN(L1_SPM_ELG_ACT) FROM T_PERIODIC_VALUE_LOG),
Q4max = (SELECT MAX(L1_SPM_ELG_ACT) FROM T_PERIODIC_VALUE_LOG),
Q4avg = (SELECT AVG(L1_SPM_ELG_ACT) FROM T_PERIODIC_VALUE_LOG),
END_TIME = SYSTIMESTAMP,
Q4_FLAG = 1;

END IF;

END;
/