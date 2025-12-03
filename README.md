create or replace NONEDITIONABLE FUNCTION f_nannow_second_telgrm(curr_date DATE) RETURN VARCHAR2 IS
 
							ls_id_tgrm_mesg VARCHAR2(26);
							ls_curr_date_time VARCHAR2(19);
							ln_nsec_seqn_srno NUMBER(6);

BEGIN

				SELECT TO_CHAR(SYSDATE,'YYYY-MM-DD-HH24.MI.SS')
				INTO ls_curr_date_time
				FROM  dual;

				SELECT nsec_seqn_srno_mesg.NEXTVAL
				INTO ln_nsec_seqn_srno
				FROM  dual;

				ls_id_tgrm_mesg:=ls_curr_date_time||'.'||ln_nsec_seqn_srno;

				RETURN(ls_id_tgrm_mesg);

END;


Function F_NANNOW_SECOND_TELGRM compiled

LINE/COL  ERROR
--------- -------------------------------------------------------------
13/5      PL/SQL: SQL Statement ignored
13/12     PL/SQL: ORA-02289: sequence does not exist
Errors: check compiler log
