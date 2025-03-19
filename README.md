public async void  DatabaseConnect()
{
    try
    {
        Con = new OracleConnection(ConnectioString);
        Con.Open();
    }
    catch (Exception ex)
    {
        if (ex.Message == "Connection request timed out")
        {

        }

        // Trigger the event instead of showing a message box
        // OnDatabaseError?.Invoke(ex.Message);
    }
}
