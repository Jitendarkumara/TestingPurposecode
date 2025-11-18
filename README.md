public int SenToL3(string coilId ,string DCoil)
{
    OracleParameter[] parameters = new OracleParameter[] {
      //new OracleParameter("COIL_WEIGHT", coilWeight),
      new OracleParameter("p_coil_id", coilId),
        new OracleParameter("p_daughter_coil", DCoil)
    };
    string ProcedureName = "p_production_reporting"; 
    if (dbContext.ExecuteProcedure(parameters, ProcedureName))
    {
        //LogWriter.LogWriter.WriteLine($"Coil data of {coilId} sent to Level3 successfully.");
        return 1;
    }
    else
    {
        //LogWriter.LogWriter.WriteLine($"Error occured while sending coil data of {coilId} to Level3.");
        return 0;
    }

}



create or replace PROCEDURE p_production_reporting (
    p_coil_id IN VARCHAR2,p_daughter_coil in varchar2
) AS

ID_MSG VARCHAR2(26):= TO_CHAR(SYSDATE, 'YYYY-MM-DD-HH24.MI.SS') || '.' ||
                LPAD(ABS(MOD(DBMS_RANDOM.RANDOM, 1000000)), 6, '0') ;
BEGIN

kindly 
