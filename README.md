using System;
using System.Data.OracleClient;
using System.Threading.Tasks;
using System.Windows.Forms;

public class DatabaseService
{
    private OracleConnection Con;
    private readonly string ConnectionString = "YourConnectionString";
    public event Action<string> OnDatabaseError; // Event for error handling

    public async Task DatabaseConnectAsync()
    {
        try
        {
            Con = new OracleConnection(ConnectionString);

            // Run OpenAsync to avoid UI freeze
            await Task.Run(() => Con.Open());

            Console.WriteLine("Database connected successfully.");
        }
        catch (Exception ex)
        {
            if (ex.Message.Contains("Connection request timed out"))
            {
                Console.WriteLine("Timeout occurred. Retrying...");
            }

            // Trigger an event instead of showing a message box
            OnDatabaseError?.Invoke(ex.Message);
        }
    }
}