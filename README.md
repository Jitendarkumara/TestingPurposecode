  private void FillDelayGridShift(string selectedShift, DateTime selectedDate)
  {
      string query;
      bool isAllShifts = selectedShift.Equals("All", StringComparison.OrdinalIgnoreCase);

      if (isAllShifts || selectedShift == "C")
      {
          query = @"SELECT DELAY_ID, SHIFT, COIL_NUMBER,LINE_START_TIME, LINE_STOP_TIME, 
                   DELAY_DURATION, REASON_CODE, OPERATOR_ID, REASON_DESCRIPTION, REMARKS, AGENCY_CODE, TOM
            FROM T_DELAY_SHEET_TEM
            WHERE DELAY_DURATION > 3 
              AND LINE_START_TIME BETWEEN :StartDateTime AND :EndDateTime"
                    + (isAllShifts ? "" : " AND SHIFT = :Shift ") + @"
            ORDER BY LINE_STOP_TIME";
      }
      else
      {
          // Use date range instead of TRUNC for performance and accuracy
          query = @"SELECT DELAY_ID, SHIFT, COIL_NUMBER,LINE_START_TIME, LINE_STOP_TIME, 
                   DELAY_DURATION, REASON_CODE, OPERATOR_ID, REASON_DESCRIPTION, REMARKS, AGENCY_CODE, TOM
            FROM T_DELAY_SHEET_TEM
            WHERE DELAY_DURATION > 3 
              AND SHIFT = :Shift 
              AND LINE_START_TIME >= :StartDate AND LINE_START_TIME < :EndDate
            ORDER BY LINE_STOP_TIME";
      }

      try
      {
          Db.DatabaseConnect();

          using (OracleDataAdapter da = new OracleDataAdapter(query, Db.Con))
          {
              if (isAllShifts)
              {
                  DateTime start = selectedDate.Date.AddHours(6);               // 6 AM of selected date
                  DateTime end = selectedDate.AddDays(1).Date.AddHours(6);     // 6 AM of next day

                  da.SelectCommand.Parameters.Add("StartDateTime", OracleDbType.Date).Value = start;
                  da.SelectCommand.Parameters.Add("EndDateTime", OracleDbType.Date).Value = end;
              }
              else if (selectedShift == "C")
              {
                  DateTime start = selectedDate.Date.AddHours(22);             // 10 PM of selected date
                  DateTime end = selectedDate.AddDays(1).Date.AddHours(6);     // 6 AM of next day

                  da.SelectCommand.Parameters.Add("StartDateTime", OracleDbType.Date).Value = start;
                  da.SelectCommand.Parameters.Add("EndDateTime", OracleDbType.Date).Value = end;
                  da.SelectCommand.Parameters.Add("Shift", OracleDbType.Varchar2).Value = selectedShift;
              }
              else
              {
                  DateTime start = selectedDate.Date;
                  DateTime end = selectedDate.Date.AddDays(1);

                  da.SelectCommand.Parameters.Add("Shift", OracleDbType.Varchar2).Value = selectedShift;
                  da.SelectCommand.Parameters.Add("StartDate", OracleDbType.Date).Value = start;
                  da.SelectCommand.Parameters.Add("EndDate", OracleDbType.Date).Value = end;
              }

              dtshift = new DataTable();
              da.Fill(dtshift);
              dgvDelay.DataSource = dtshift;
          }
      }
      catch (Exception ex)
      {
        //  MessageBox.Show("Error: " + ex.Message, "Data Load Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
      }
      finally
      {
          Db.ConClose();
      }

      dgvDelay.AutoGenerateColumns = false;
  }
      
