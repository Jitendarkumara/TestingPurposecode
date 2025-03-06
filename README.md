using Oracle.ManagedDataAccess.Client;
using System;

namespace PDO
{
    public class DBhelper
    {
        public OracleConnection Con;
        public string ConnectioString = System.Configuration.ConfigurationManager.ConnectionStrings["ServiceURL"].ToString();

        // Define an event to notify when an error occurs
        public event Action<string> OnDatabaseError;

        public void DatabaseConnect()
        {
            try
            {
                Con = new OracleConnection(ConnectioString);
                Con.Open();
            }
            catch (Exception ex)
            {
                // Trigger the event instead of showing a message box
                OnDatabaseError?.Invoke(ex.Message);
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
}