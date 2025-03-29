public void DatabaseConnect()
{
    try
    {
        using (OracleConnection con = new OracleConnection(ConnectionString))
        {
            con.Open();
            Console.WriteLine("Database connection established successfully.");
        }
    }
    catch (Exception ex)
    {
        LogErrorToFile(ex.Message, "DatabaseConnect");
        Console.WriteLine("Database connection failed: " + ex.Message);
    }
}