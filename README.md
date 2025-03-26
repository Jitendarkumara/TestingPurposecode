 public bool DatabaseConnectCheck()
 {
     try
     {
         Con = new OracleConnection(ConnectioString);
         Con.Open(); // Run Open() in a background thread
         return true;
     }
     catch (Exception ex)
     {
        return false;
         //  OnDatabaseError?.Invoke(ex.Message); // Notify any subscribed forms
     }
 }
