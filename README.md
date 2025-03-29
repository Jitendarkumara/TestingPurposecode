using System;
using System.Data;
using Oracle.ManagedDataAccess.Client; // Ensure you have Oracle.ManagedDataAccess installed.

public class DatabaseHandler
{
    private string ConnectionString = "User Id=myUsername;Password=myPassword;Data Source=myOracleDB";

    public void DatabaseConnect()
    {
        OracleConnection con = null;

        try
        {
            // Create new connection object
            con = new OracleConnection(ConnectionString);

            // Check if connection is valid before opening
            if (con.State != ConnectionState.Open)
            {
                con.Open();
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
        }
        catch (Exception ex)
        {
            Console.WriteLine("General error: " + ex.Message);
            LogErrorToFile(ex.ToString(), "DatabaseConnect");
        }
        finally
        {
            // Ensure the connection is closed and disposed properly
            if (con != null)
            {
                if (con.State == ConnectionState.Open)
                {
                    con.Close();
                    Console.WriteLine("Database connection closed.");
                }
                con.Dispose();
            }
        }
    }

    private void LogErrorToFile(string errorMessage, string functionName)
    {
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