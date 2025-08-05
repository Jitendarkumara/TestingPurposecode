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
                cmd.BindByName = true;

                // Input parameters
                cmd.Parameters.Add("P_COIL_ID", OracleDbType.Varchar2, 50).Value = Coil_id;
                cmd.Parameters.Add("MIN_RUNID", OracleDbType.Varchar2, 50).Value = Min_RUNID;
                cmd.Parameters.Add("MAX_RUNID", OracleDbType.Varchar2, 50).Value = Max_RUNID;

                // Output parameter
                OracleParameter output = new OracleParameter("ISUCCESS", OracleDbType.Varchar2, 10);
                output.Direction = ParameterDirection.Output;
                cmd.Parameters.Add(output);

                cmd.ExecuteNonQuery();

                string result = output.Value?.ToString()?.Trim().ToUpper();
                if (result == "S")
                {
                    isSuccessData = true;
                    LogWriter.WriteLine($"[SUCCESS] Data inserted for Coil: {Coil_id}");
                }
                else
                {
                    LogWriter.WriteLine($"[FAILURE] Insert failed for Coil: {Coil_id}, Return: {result}");
                }
            }
        }
    }
    catch (Exception ex)
    {
        LogWriter.WriteLine($"[EXCEPTION] {ex.Message}");
    }

    return isSuccessData;
}