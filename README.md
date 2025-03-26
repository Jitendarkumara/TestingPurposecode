public bool DatabaseConnectCheck()
{
    try
    {
        using (OracleConnection con = new OracleConnection(ConnectioString))
        {
            con.Open();
            return true;
        }
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

private System.Timers.Timer dbCheckTimer;

public void StartDatabaseCheckTimer()
{
    dbCheckTimer = new System.Timers.Timer(1000); // 1 second
    dbCheckTimer.Elapsed += async (sender, e) => await CheckDatabaseAsync();
    dbCheckTimer.AutoReset = true;
    dbCheckTimer.Enabled = true;
}

private async Task CheckDatabaseAsync()
{
    bool isConnected = await DatabaseConnectCheckAsync();
    if (!isConnected)
    {
        Console.WriteLine("Database connection lost!"); // Log or update UI safely
    }
}