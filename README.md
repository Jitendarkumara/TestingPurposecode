 private string GetL3ReadFlag(string daughterCoilId)
 {
     string l3ReadFlag = string.Empty;

     // No need to set up a connection string if you're already connected
     string query = "SELECT l3_read_flag FROM T_PDO_INFO WHERE CGD_ID_COIL = :DaughterCoilId";

     // Use OracleCommand with OracleParameter to prevent SQL injection
     using (OracleCommand command = new OracleCommand(query, Db.Con))
     {
         command.Parameters.Add(new OracleParameter("DaughterCoilId", daughterCoilId));

         object result = command.ExecuteScalar();
         if (result != null)
         {
             l3ReadFlag = result.ToString();
         }
     }

     return l3ReadFlag;
 }
