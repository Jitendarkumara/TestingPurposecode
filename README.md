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
                cmd.BindByName = true; // CRITICAL!

                // Add input parameters
                cmd.Parameters.Add("P_COIL_ID", OracleDbType.Varchar2).Value = Coil_id;
                cmd.Parameters.Add("MIN_RUNID", OracleDbType.Varchar2).Value = Convert.ToInt32(Min_RUNID).ToString();
                cmd.Parameters.Add("MAX_RUNID", OracleDbType.Varchar2).Value = Convert.ToInt32(Max_RUNID).ToString();

                // Add output parameter
                OracleParameter outParam = new OracleParameter("ISSUCESS", OracleDbType.Varchar2, 10)
                {
                    Direction = ParameterDirection.Output
                };
                cmd.Parameters.Add(outParam);

                // Execute
                cmd.ExecuteNonQuery();

                // Read result
                string result = outParam.Value?.ToString()?.Trim().ToUpper();

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