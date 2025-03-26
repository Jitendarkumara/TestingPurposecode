  private async Task CheckDatabaseAsync()
  {
      bool isConnected =  Db.DatabaseConnectCheck();
      if (!isConnected)
      {
          timerText.Enabled = false;
          //Console.WriteLine("Database connection lost!"); // Log or update UI safely

      }
      else
      {
          timerText.Enabled = true;
   
      }
  }
