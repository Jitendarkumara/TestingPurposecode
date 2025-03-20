 public class DBhelper
 {
     public OracleConnection Con;
     public string ConnectioString = System.Configuration.ConfigurationManager.ConnectionStrings["ServiceURL"].ToString();
     private System.Windows.Forms.Timer _timer;
     // Define an event to notify when an error occurs
     public event Action<string> OnDatabaseError;
    MasterPage MasterPage1 = new MasterPage();
     

     public async Task DatabaseConnect()
     {
         try
         {
             Con = new OracleConnection(ConnectioString);
             await Task.Run(() => Con.Open()); // Run Open() in a background thread
         }
         catch (Exception ex)
         {
             if (ex.Message.Contains("Connection request timed out"))
             {
                 MasterPage1.AutoPopTimer.Stop();
               
                 MessageBox.Show("Database connection timed out. Timer stopped.", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
             }

             OnDatabaseError?.Invoke(ex.Message); // Notify any subscribed forms
         }
     }
 
   
     public void ConClose()
     {
         if (Con != null)
         {
             Con.Close();
             Con.Dispose();
         }
     }
 }
