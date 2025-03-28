     public void DatabaseConnect()
     {
         try
         {
             Con = new OracleConnection(ConnectioString);
             Con.Open();
         }
         catch (Exception ex)
         {
             StackTrace st = new StackTrace(ex, true);
          int lineNumber=   st.GetFrame(0).GetFileLineNumber();
             LogErrorToFile(ex.Message,lineNumber);
             // Trigger the event instead of showing a message box
             // OnDatabaseError?.Invoke(ex.Message);
         }
     }
     public void LogErrorToFile(string errorMessage,int Line)
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
             File.AppendAllText(logFilePath, errorMessage+"Line Number"+ Line+"Time" + $"{DateTime.Now}\n");
         }
         catch (Exception ex)
         {
             Console.WriteLine("Error writing to log file: " + ex.Message);
         }
     }
