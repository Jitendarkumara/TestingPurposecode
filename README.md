public bool DatabaseConnectCheck()
{
    using (OracleConnection con = new OracleConnection(ConnectioString))
    {
        try
        {
            con.Open();
            return true; // Connection successful
        }
        catch
        {
            return false; // Connection failed
        }
    }
}