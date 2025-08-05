create or replace PROCEDURE P_PROCESS_DATA_INSERT 
(
P_COIL_ID VARCHAR2,
MIN_RUNID VARCHAR2,
MAX_RUNID VARCHAR2,
ISSUCESS OUT VARCHAR2
) AS 
--P_COIL_ID VARCHAR2(20);
START_TIME TIMESTAMP:= SYSDATE;
H_STM_ID VARCHAR2(50);
BEGIN
--SELECT COIL_ID INTO P_COIL_ID FROM T_SPM_COIL_PROCESSING WHERE LOCATION ='POR'; --AND FLAG_ID=110;
H_STM_ID:='SELECT 1';
--SELECT DATE_TIME INTO START_TIME FROM SPML2.T_EVENT_LOG WHERE COIL_ID=P_COIL_ID AND ID_APP_TAG='L1_COIL_START' ORDER BY DATE_TIME ASC FETCH FIRST 1 ROW ONLY;

INSERT INTO T_SPM_PROCESS_DATA_LEVEL2
(
MSG_ID,
SOURCE_APPLN,
DESTN_APPLN,
ELONGATION_ACT,
COIL_ID
)

SELECT 
F_NANNOW_SECOND_TELGRM(SYSDATE),
'SPML2',
'C3DIS',
--ROUND(AVG(L1_ELG_ACT),2),
1,
P_COIL_ID

FROM T_PERIODIC_VALUE_LOG
    WHERE RUN_ID BETWEEN MIN_RUNID AND MAX_RUNID
      AND Mill_speed > 15;


ISSUCESS:='S';
EXCEPTION
     WHEN NO_DATA_FOUND THEN
       NULL;
    --   ISSUCESS:='F';
     WHEN OTHERS THEN
     --ISSUCESS:='F';
       -- HANDL_EERROR('SPM_LEVEL2','P_SETPOINT_UPDATE_HMI ',h_stm_id,sqlcode,substr(sqlerrm(sqlcode),1,100),null,'T_SPM_PROCESS_DATA_LEVEL2');
     COMMIT;
     COMMIT;
END P_PROCESS_DATA_INSERT;
-------C# code-----
   public bool PDOgeneration(string Coil_id, string Min_RUNID, string Max_RUNID)
   {
       bool IssucessPDO;
       bool IsScuessData;
       try
       {
           using (OracleConnection oc = new OracleConnection())
           {
               //App.config Data Source
               oc.ConnectionString = Constr.ConnectionString;// "User ID=bindb; Password=bindb; Data Source=MyDataSource;";

               oc.Open();

               using (OracleCommand cmd = new OracleCommand("P_PROCESS_DATA_INSERT", oc))
               {
                   cmd.Parameters.Add("P_COIL_ID", OracleDbType.Varchar2).Value = Coil_id;
                   cmd.Parameters.Add("MIN_RUNID", OracleDbType.Varchar2).Value = Min_RUNID;
                   cmd.Parameters.Add("MAX_RUNID", OracleDbType.Varchar2).Value = Max_RUNID;
                   OracleParameter outParams = new OracleParameter("ISSUCESS", OracleDbType.Varchar2, 10);
                   outParams.Direction = ParameterDirection.Output;
                   cmd.CommandType = CommandType.StoredProcedure;
                   cmd.CommandType = CommandType.StoredProcedure;
                   cmd.Parameters.Add(outParams);
                   cmd.ExecuteNonQuery();
                   string sucess = outParams.Value.ToString();
                   if (sucess.ToUpper() == "S")
                   {
                       IsScuessData = true;
                       LogWriter.WriteLine("Process data Inserted");

                   }
                   else
                   {
                       IsScuessData = false;
                       LogWriter.WriteLine("Failed to enter Process Data");
                   }


               }
           }
           return IsScuessData;
       }
       catch (Exception ex)
       {
           LogWriter.WriteLine(ex.ToString());
           return false;

       }
   }
      
