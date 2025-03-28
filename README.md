public void LogErrorToFile(string errorMessage, string stackTrace)
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
        File.AppendAllText(logFilePath, $"Error: {errorMessage}\nStack Trace: {stackTrace}\nTime: {DateTime.Now}\n\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error writing to log file: " + ex.Message);
    }
}

catch (Exception ex)
{
    string stackTrace = ex.StackTrace;
    LogErrorToFile(ex.Message, stackTrace);
}