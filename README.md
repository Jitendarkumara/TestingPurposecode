public void DatabaseConnect()
{
    try
    {
        if (Con == null)
        {
            Con = new OracleConnection(ConnectioString);
        }

        if (Con.State != ConnectionState.Open)
        {
            Con.Open();
            Console.WriteLine("Database connection established successfully.");
        }
        else
        {
            Console.WriteLine("Connection is already open.");
        }
    }
    catch (InvalidOperationException ex)
    {
        Console.WriteLine("Invalid operation: " + ex.Message);
        LogErrorToFile(ex.ToString(), "DatabaseConnect");
    }
    catch (OracleException ex)
    {
        Console.WriteLine("Oracle database error: " + ex.Message);
        LogErrorToFile(ex.ToString(), "DatabaseConnect");
        ConClose(); // Ensure proper cleanup
    }
    catch (Exception ex)
    {
        Console.WriteLine("General error: " + ex.Message);
        LogErrorToFile(ex.ToString(), "DatabaseConnect");
        ConClose();
    }
}