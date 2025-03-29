   public void DatabaseConnect()
   {
       //try
       //{
       //    Con = new OracleConnection(ConnectioString);
       //    Con.Open();
       //}
       //catch (Exception ex)
       //{
       //    {
       //        LogErrorToFile(ex.Message, "DatabaseConnect");
       //        if (Con != null && Con.State == System.Data.ConnectionState.Open)
       //        {
       //            Con.Close();
       //        }

             
       //    }
       //}

       using (OracleConnection con = new OracleConnection(ConnectioString))
       {
           try
           {
               con.Open();
             //  return true; // Connection successful
           }
           catch
           {
              // return false; // Connection failed
           }
       }

   }
