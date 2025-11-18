public int SenToL3(string coilId, string DCoil)
{
    OracleParameter[] parameters = new OracleParameter[]
    {
        new OracleParameter("p_coil_id", coilId),
        new OracleParameter("p_daughter_coil", DCoil)   // <-- FIXED
    };

    string ProcedureName = "p_production_reporting";  // must match PL/SQL name

    if (dbContext.ExecuteProcedure(parameters, ProcedureName))
    {
        return 1;
    }
    else
    {
        return 0;
    }
}