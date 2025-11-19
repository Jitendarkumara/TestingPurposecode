  private void GetTblTrackingPage()
  {
      DBContext dbContext1 = new DBContext();
      dtLine = dbContext1.GetlineData();
      dtLine.AcceptChanges();
      if (dtLine != null)
      {
          
          if (dtLine.Rows[0][1] != DBNull.Value)
          {
              CoilPosition.dtProcLine.Rows[0][1] = dtLine.Rows[0][1];
              CoilPosition.dtProcLine.Rows[0][2] = dtLine.Rows[0][2];
              CoilPosition.dtProcLine.Rows[0][3] = dtLine.Rows[0][3];
          }
          else
          {
              CoilPosition.dtProcLine.Rows[0][1] = DBNull.Value;
              CoilPosition.dtProcLine.Rows[0][2] = DBNull.Value;
              CoilPosition.dtProcLine.Rows[0][3] = DBNull.Value;
          }

          if (dtLine.Rows[1][1] != DBNull.Value)
          {
              //CoilPosition.dtProcLine.Rows[4][1] = dtLine.Rows[1][1];
              //CoilPosition.dtProcLine.Rows[4][2] = dtLine.Rows[1][2];
              //CoilPosition.dtProcLine.Rows[4][3] = dtLine.Rows[1][3];
              CoilPosition.dtProcLine.Rows[1][1] = dtLine.Rows[1][1];
              CoilPosition.dtProcLine.Rows[1][2] = dtLine.Rows[1][2];
              CoilPosition.dtProcLine.Rows[1][3] = dtLine.Rows[1][3];
          }
          else
          {
              //CoilPosition.dtProcLine.Rows[4][1] = DBNull.Value;
              //CoilPosition.dtProcLine.Rows[4][2] = DBNull.Value;
              //CoilPosition.dtProcLine.Rows[4][3] = DBNull.Value;
              CoilPosition.dtProcLine.Rows[1][1] = DBNull.Value;
              CoilPosition.dtProcLine.Rows[1][2] = DBNull.Value;
              CoilPosition.dtProcLine.Rows[1][3] = DBNull.Value;
          }
          CoilPosition.dtProcLine.AcceptChanges();

      }
      else
      {
          MessageBox.Show("Bad data from DataBase", "Info");
      }
  }
