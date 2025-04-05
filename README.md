using Oracle.ManagedDataAccess.Client;
using System;
using System.Data;
using System.Diagnostics;
using System.IO;

namespace PDO
{
    public class DBhelper
    {
        public OracleConnection Con;
        public string ConnectioString = System.Configuration.ConfigurationManager.ConnectionStrings["ServiceURL"].ToString();

        // Define an event to notify when an error occurs
        public event Action<string> OnDatabaseError;

        //public void DatabaseConnect()
        //{
        //    try
        //    {
        //        Con = new OracleConnection(ConnectioString);
        //        Con.Open();
        //    }
        //    catch (Exception ex)
        //    {
        //        {
        //            LogErrorToFile(ex.Message, "DatabaseConnect");
        //            if (Con != null && Con.State == System.Data.ConnectionState.Open)
        //            {
        //                Con.Close();
        //            }


        //        }
        //    }


        //}
        //public void DatabaseConnect()
        //{
        //    try
        //    {
        //        using (OracleConnection con = new OracleConnection(ConnectioString))
        //        {
        //            if (con.State == System.Data.ConnectionState.Closed)
        //            {
        //                con.Open();
        //                Console.WriteLine("Database connection established successfully.");
        //            }
        //        }
        //    }
        //    catch (Exception ex)
        //    {
        //        LogErrorToFile(ex.Message, "DatabaseConnect");
        //        Console.WriteLine("Database connection failed: " + ex.Message);
        //    }
        //}


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
        public void LogErrorToFile(string errorMessage, string Method)
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

                // Append the error message and stack trace
                File.AppendAllText(logFilePath, $"Error: {errorMessage}\nMethod: {Method}\nTime: {DateTime.Now}\n\n");
            }
            catch (Exception ex)
            {
                Console.WriteLine("Error writing to log file: " + ex.Message);
            }
        }
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
                    if (con != null)
                    {
                        if (con.State == ConnectionState.Open)
                        {
                            con.Close();
                            Console.WriteLine("Database connection closed.");
                        }
                        con.Dispose();
                    }
                    return false; // Connection failed
                }

            }
        }

        public void ConClose()
        {
            if (Con != null && Con.State == ConnectionState.Open)
            {
                Con.Close();
                Console.WriteLine("Database connection closed.");
            }

            if (Con != null)
            {
                Con.Dispose();
                Con = null; // Prevent using disposed object
            }
        }
    }
}
