private void FillDelayGridShift(string selectedShift, DateTime selectedDate)
{
    string query;
    bool isAllShifts = selectedShift.Equals("All", StringComparison.OrdinalIgnoreCase);

    if (isAllShifts || selectedShift == "C")
    {
        query = @"SELECT DELAY_ID, SHIFT, COIL_NUMBER, LINE_STOP_TIME, LINE_START_TIME,
                         DELAY_DURATION, REASON_CODE, OPERATOR_ID, REASON_DESCRIPTION, REMARKS, AGENCY_CODE, TOM
                  FROM T_DELAY_SHEET_TEM
                  WHERE LINE_START_TIME BETWEEN :StartDateTime AND :EndDateTime
                  " + (isAllShifts ? "" : " AND SHIFT = :Shift ") + @"
                  ORDER BY LINE_STOP_TIME";
    }
    else
    {
        query = @"SELECT DELAY_ID, SHIFT, COIL_NUMBER, LINE_STOP_TIME, LINE_START_TIME,
                         DELAY_DURATION, REASON_CODE, OPERATOR_ID, REASON_DESCRIPTION, REMARKS, AGENCY_CODE, TOM
                  FROM T_DELAY_SHEET_TEM
                  WHERE SHIFT = :Shift AND TRUNC(LINE_START_TIME) = TRUNC(:SelectedDate)
                  ORDER BY LINE_STOP_TIME";
    }

    try
    {
        Db.DatabaseConnect();

        using (OracleDataAdapter da = new OracleDataAdapter(query, Db.Con))
        {
            if (isAllShifts)
            {
                DateTime start = selectedDate.Date.AddHours(6);
                DateTime end = selectedDate.AddDays(1).Date.AddHours(6);

                da.SelectCommand.Parameters.Add("StartDateTime", OracleDbType.Date).Value = start;
                da.SelectCommand.Parameters.Add("EndDateTime", OracleDbType.Date).Value = end;
            }
            else if (selectedShift == "C")
            {
                DateTime start = selectedDate.Date.AddHours(22); // 10 PM same day
                DateTime end = selectedDate.AddDays(1).Date.AddHours(6); // 6 AM next day

                da.SelectCommand.Parameters.Add("StartDateTime", OracleDbType.Date).Value = start;
                da.SelectCommand.Parameters.Add("EndDateTime", OracleDbType.Date).Value = end;
                da.SelectCommand.Parameters.Add("Shift", OracleDbType.Varchar2).Value = selectedShift;
            }
            else
            {
                da.SelectCommand.Parameters.Add("Shift", OracleDbType.Varchar2).Value = selectedShift;
                da.SelectCommand.Parameters.Add("SelectedDate", OracleDbType.Date).Value = selectedDate.Date;
            }

            dtshift = new DataTable();
            da.Fill(dtshift);
            dgvDelay.DataSource = dtshift;
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error: " + ex.Message);
    }
    finally
    {
        Db.ConClose();
    }

    dgvDelay.AutoGenerateColumns = false;
}