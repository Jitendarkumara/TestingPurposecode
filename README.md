public bool PDOgeneration(string Coil_id, string Min_RUNID, string Max_RUNID)
{
    bool isSuccessData = false;

    try
    {
        using (OracleConnection oc = new OracleConnection(Constr.ConnectionString))
        {
            oc.Open();

            using (OracleCommand cmd = new OracleCommand("P_PROCESS_DATA_INSERT", oc))
            {
                cmd.CommandType = CommandType.StoredProcedure;
                cmd.BindByName = true; // Make sure parameters are bound by name

                // Add input parameters with size
                cmd.Parameters.Add("P_COIL_ID", OracleDbType.Varchar2, 20).Value = Coil_id;
                cmd.Parameters.Add("MIN_RUNID", OracleDbType.Varchar2, 20).Value = Min_RUNID;
                cmd.Parameters.Add("MAX_RUNID", OracleDbType.Varchar2, 20).Value = Max_RUNID;

                // Add output parameter with size and direction
                OracleParameter outParam = new OracleParameter("ISUCCESS", OracleDbType.Varchar2, 10)
                {
                    Direction = ParameterDirection.Output
                };
                cmd.Parameters.Add(outParam);

                // Execute
                cmd.ExecuteNonQuery();

                // Read result safely
                string result = (outParam.Value ?? "").ToString().Trim().ToUpper();

                if (result == "S")
                {
                    isSuccessData = true;
                    LogWriter.WriteLine($"[SUCCESS] Data inserted for Coil: {Coil_id}");
                }
                else
                {
                    isSuccessData = false;
                    LogWriter.WriteLine($"[FAILURE] Insert failed for Coil: {Coil_id}. Return: {result}");
                }
            }
        }
    }
    catch (Exception ex)
    {
        LogWriter.WriteLine($"[EXCEPTION] {ex.Message}");
        LogWriter.WriteLine(ex.ToString());
        isSuccessData = false;
    }

    return isSuccessData;
}