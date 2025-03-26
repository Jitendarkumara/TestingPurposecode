private void LogErrorToFile(string errorMessage)
{
    try
    {
        string desktopPath = Environment.GetFolderPath(Environment.SpecialFolder.Desktop);
        string logFilePath = Path.Combine(desktopPath, "DatabaseErrorLog.txt");

        if (!File.Exists(logFilePath))
        {
            // Create new file and write header
            string header = $"Database Error Log\nCreated on: {DateTime.Now}\n\n";
            File.WriteAllText(logFilePath, header);
        }

        // Append the error message to the file
        File.AppendAllText(logFilePath, errorMessage);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error writing to log file: " + ex.Message);
    }
}