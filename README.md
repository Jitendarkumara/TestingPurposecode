using System;
using System.Data;
using System.Data.OracleClient;
using System.Threading.Tasks;
using System.Windows.Forms;

public class DBhelper
{
    public OracleConnection Con;
    public string ConnectioString = System.Configuration.ConfigurationManager.ConnectionStrings["ServiceURL"].ToString();
    public event Action<string> OnDatabaseError; // Event to notify forms about errors

    private System.Windows.Forms.Timer _timer; // Reference to the timer

    // Constructor to pass the timer instance from the form
    public DBhelper(System.Windows.Forms.Timer timer)
    {
        _timer = timer;
    }

    public async Task DatabaseConnectAsync()
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
                _timer.Stop(); // Stop the timer if timeout occurs
                MessageBox.Show("Database connection timed out. Timer stopped.", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }

            OnDatabaseError?.Invoke(ex.Message); // Notify any subscribed forms
        }
    }
}