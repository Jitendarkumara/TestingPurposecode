 public bool ExecuteProcedure(OracleParameter[] parameters, string ProcedureName)
 {
     using (OracleConnection connection = new OracleConnection(ConnectionString))
     {
         try
         {
             connection.Open();
             OracleCommand cmd = new OracleCommand();
             cmd.Connection = connection;
             cmd.CommandType = CommandType.StoredProcedure;
             cmd.CommandText = ProcedureName;
             cmd.Parameters.AddRange(parameters);
             OracleParameter outparam = new OracleParameter("ISSUCCESS", OracleDbType.Varchar2, 10);
             outparam.Direction = ParameterDirection.Output;
             cmd.Parameters.Add(outparam);

             cmd.ExecuteNonQuery();
             if (outparam.Value.ToString().ToUpper() == "S")
             {
                 return true;
             }
             else
             {
                 return false;
             }
         }
         catch (Exception ex)
         {
             //Console.WriteLine("Error Occured: " + ex.Message);
             return false;
         }
         finally
         {
             if (connection.State == ConnectionState.Open)
             {
                 connection.Close();
             }
         }

     }
 }
