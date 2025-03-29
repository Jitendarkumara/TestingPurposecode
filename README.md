using System;
using System.Data;
using Oracle.ManagedDataAccess.Client; // Ensure you have Oracle Managed Data Access installed.

public class DatabaseHandler
{
    private string ConnectionString = "User Id=myUsername;Password=myPassword;Data Source=myOracleDB";

    public void DatabaseConnect()
    {
        try
        {
            using (OracleConnection con = new OracleConnection(ConnectionString))
            {
                // Ensure the connection is closed before opening
                if (con.State == ConnectionState.Closed)
                {
                    con.Open();
                    Console.WriteLine("Database connection established successfully.");
                }
                else
                {
                    Console.WriteLine("Connection is already open.");
                }
            }
        }
        catch (InvalidOperationException ex)
        {
            Console.WriteLine("Invalid operation: " + ex.Message);
            LogErrorToFile(ex.Message, "DatabaseConnect");
        }
        catch (OracleException ex)
        {
            Console.WriteLine("Oracle database error: " + ex.Message);
            LogErrorToFile(ex.Message, "DatabaseConnect");
        }
        catch (Exception ex)
        {
            Console.WriteLine("General error: " + ex.Message);
            LogErrorToFile(ex.Message, "DatabaseConnect");
        }
    }

    private void LogErrorToFile(string errorMessage, string functionName)
    {
        // Simple logging to a text file (Modify path as needed)
        string logFilePath = @"C:\Logs\DatabaseErrors.log";

        try
        {
            using (System.IO.StreamWriter writer = new System.IO.StreamWriter(logFilePath, true))
            {
                writer.WriteLine($"{DateTime.Now} - {functionName}: {errorMessage}");
            }
        }
        catch
        {
            Console.WriteLine("Error logging failed.");
        }
    }
}