using System;
using System.Configuration;
using System.Data;
using Oracle.ManagedDataAccess.Client; // Make sure you have Oracle.ManagedDataAccess installed
using System.IO;

public class DBhelper
{
    private readonly string ConnectionString;

    public DBhelper()
    {
        ConnectionString = ConfigurationManager.ConnectionStrings["ServiceURL"].ToString();
    }

    // 🔹 Fetch data into DataTable
    public DataTable ExecuteQuery(string query)
    {
        DataTable dt = new DataTable();

        try
        {
            using (OracleConnection con = new OracleConnection(ConnectionString))
            {
                con.Open();
                using (OracleDataAdapter da = new OracleDataAdapter(query, con))
                {
                    da.Fill(dt);
                }
            }
        }
        catch (Exception ex)
        {
            LogErrorToFile(ex.ToString(), "ExecuteQuery");
            throw;
        }

        return dt;
    }

    // 🔹 Execute INSERT/UPDATE/DELETE
    public int ExecuteNonQuery(string query)
    {
        try
        {
            using (OracleConnection con = new OracleConnection(ConnectionString))
            {
                con.Open();
                using (OracleCommand cmd = new OracleCommand(query, con))
                {
                    return cmd.ExecuteNonQuery();
                }
            }
        }
        catch (Exception ex)
        {
            LogErrorToFile(ex.ToString(), "ExecuteNonQuery");
            throw;
        }
    }

    // 🔹 Check if DB is reachable
    public bool DatabaseConnectCheck()
    {
        try
        {
            using (OracleConnection con = new OracleConnection(ConnectionString))
            {
                con.Open();
                return true;
            }
        }
        catch
        {
            return false;
        }
    }

    // 🔹 Log errors to file
    private void LogErrorToFile(string errorMessage, string method)
    {
        try
        {
            string desktopPath = Environment.GetFolderPath(Environment.SpecialFolder.Desktop);
            string logFilePath = Path.Combine(desktopPath, "DatabaseErrorLog.txt");

            if (!File.Exists(logFilePath))
            {
                string header = $"Database Error Log\nCreated on: {DateTime.Now}\n\n";
                File.WriteAllText(logFilePath, header);
            }

            File.AppendAllText(logFilePath, $"Error: {errorMessage}\nMethod: {method}\nTime: {DateTime.Now}\n\n");
        }
        catch
        {
            // Swallow logging errors silently
        }
    }
}