 public bool DatabaseConnectCheck()
 {
     try
     {
         Con = new OracleConnection(ConnectioString);
        
             Con.Open();
             return true;
        
     }
     catch (OracleException ex)
     {
         // Handle specific Oracle errors if needed (e.g., ORA-12170 for timeouts)
         return false;
     }
     catch (Exception)
     {
         return false;
     }
 }
