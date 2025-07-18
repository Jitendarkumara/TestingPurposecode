create or replace NONEDITIONABLE PROCEDURE       P_DOWNLOAD_SETPOINT

(
 TAG_EVENT SPML2.T_PLC_TAG_CONFIG.ID_APP_TAG %TYPE,
 TAG_CONDITION INT
)
IS
    v_query VARCHAR2(1000);
    RESULTVALUE VARCHAR2(20);
    --TAG_EVENT VARCHAR2(1000);
    COILID VARCHAR2(1000);
    THICKNESS_ls NUMBER(8,2);
    --COILIDCOL NUMBER;
    H_STM_ID VARCHAR2(100):=NULL;
BEGIN

   SELECT COIL_ID INTO COILID FROM SPML2.T_SPM_COIL_PROCESSING	WHERE LOCATION='POR';
   
    FOR c in (SELECT * FROM T_EVENT_DEP_CONFIG WHERE ID_APP_TAG_EVENT=TAG_EVENT ) 
    LOOP
    --SELECT COUNT(*) INTO COILIDCOL FROM ALL_TAB_COLUMNS WHERE TABLE_NAME = 'c.table_name' AND COLUMN_NAME = 'CGP_ID_COIL';
     dbms_output.put_line(c.column_name);
   

        H_STM_ID :='SELECT 6';

        v_query := 'SELECT '||c.column_name|| ' FROM '|| c.table_name || ' where COIL_ID='''|| COILID||'''' ;
 
   -- dbms_output.put_line(v_query);
    -- HANDL_EERROR('SPML2','P_DOWNLOAD_SETPOINT',h_stm_id,sqlcode,v_query,null,'T_SETPOINT_OUTPUT');

     EXECUTE IMMEDIATE v_query INTO  RESULTVALUE;
    --dbms_output.put_line(RESULTVALUE);

    H_STM_ID :='UPDATE 1';

     UPDATE T_EVENT_DEP_CONFIG SET DEP_VALUE=RESULTVALUE,DEP_VALUE_OLD=c.DEP_VALUE,UPDATE_TIME=systimestamp,WRITE_TIME = null,WRITE_STATUS = 'NT' WHERE ID_APP_TAG_EVENT=TAG_EVENT AND ID_APP_TAG_DEP=c.ID_APP_TAG_DEP;


    END LOOP;

    COMMIT;

EXCEPTION

    WHEN OTHERS THEN 
    --HANDL_EERROR('GP2 L2',' P_DOWNLOAD_SETPOINT',h_stm_id,sqlcode,substr(sqlerrm(sqlcode),1,100),null,'T_EVENT_TRACKING');
     COMMIT;
END P_DOWNLOAD_SETPOINT;

Oracle.ManagedDataAccess.Client.OracleException: 'ORA-06550: line 1, column 7:
PLS-00306: wrong number or types of arguments in call to 'P_DOWNLOAD_SETPOINT'
ORA-06550: line 1, column 7:
PL/SQL: Statement ignored'

  public void UpdateSetpoint(string eventAppTag, int eventCondition)
  {
      try
      {
          using (OracleConnection oc = new OracleConnection())
          {
              //App.config Data Source
              oc.ConnectionString = Constr.ConnectionString;// "User ID=bindb; Password=bindb; Data Source=MyDataSource;";
              oc.Open();
              using (OracleCommand cmd = new OracleCommand("P_DOWNLOAD_SETPOINT", oc))
              {
                  cmd.CommandType = CommandType.StoredProcedure;
                  cmd.Parameters.Add("TAG_EVENT", OracleDbType.Varchar2).Value = eventAppTag;
                  cmd.Parameters.Add("TAG_CONDITION", OracleDbType.Int32).Value = eventCondition;
                  //cmd.ExecuteNonQuery();
                 // LogWriter.WriteLine("Setpoint Saved");
                  OracleParameter outparams = new OracleParameter("ISSUCESS", OracleDbType.Varchar2, 5);
                  outparams.Direction = ParameterDirection.Output;
                  cmd.Parameters.Add(outparams);

                  cmd.ExecuteNonQuery();
                  if (outparams.Value.ToString() == "S")
                  {
                      LogWriter.WriteLine("Setpoint Saved");
                  }
                  else if (outparams.Value.ToString() == "F")
                  {
                      LogWriter.WriteLine("Setpoint save failed");
                  }

              }
          }
      }
      catch (Exception ex)
      {

          LogWriter.WriteLine(ex.Message);
      }
  }
       
