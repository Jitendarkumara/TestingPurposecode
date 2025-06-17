private void FillDelayGridShift(string selectedShift, DateTime selectedDate)
{
    string query;
    bool isAllShifts = selectedShift.Equals("All", StringComparison.OrdinalIgnoreCase);

    if (isAllShifts)
    {
        query = @"SELECT DELAY_ID, SHIFT, COIL_NUMBER, LINE_STOP_TIME, LINE_START_TIME,
                         DELAY_DURATION, REASON_CODE, OPERATOR_ID, REASON_DESCRIPTION, REMARKS, AGENCY_CODE, TOM
                  FROM T_DELAY_SHEET_TEM
                  WHERE LINE_START_TIME BETWEEN :StartDateTime AND :EndDateTime
                  ORDER BY LINE_STOP_TIME";
    }
    else if (selectedShift == "C")
    {
        query = @"SELECT DELAY_ID, SHIFT, COIL_NUMBER, LINE_STOP_TIME, LINE_START_TIME,
                         DELAY_DURATION, REASON_CODE, OPERATOR_ID, REASON_DESCRIPTION, REMARKS, AGENCY_CODE, TOM
                  FROM T_DELAY_SHEET_TEM
                  WHERE SHIFT = :Shift AND
                        LINE_START_TIME BETWEEN :StartDateTime AND :EndDateTime
                  ORDER BY LINE_STOP_TIME";
    }
    else
    {
        query = @"SELECT DELAY_ID, SHIFT, COIL_NUMBER, LINE_STOP_TIME, LINE_START_TIME,
                         DELAY_DURATION, REASON_CODE, OPERATOR_ID, REASON_DESCRIPTION, REMARKS, AGENCY_CODE, TOM
                  FROM T_DELAY_SHEET_TEM
                  WHERE SHIFT = :Shift AND 
                        TRUNC(LINE_START_TIME) = TRUNC(:SelectedDate)
                  ORDER BY LINE_STOP_TIME";
    }

    try
    {
        Db.DatabaseConnect();

        using (OracleDataAdapter da = new OracleDataAdapter(query, Db.Con))
        {
            if (isAllShifts)
            {
                DateTime startDateTime = selectedDate.Date.AddHours(6); // 06:00 of selected date
                DateTime endDateTime = selectedDate.Date.AddDays(1).AddHours(6); // 06:00 of next day

                da.SelectCommand.Parameters.Add("StartDateTime", OracleDbType.Date).Value = startDateTime;
                da.SelectCommand.Parameters.Add("EndDateTime", OracleDbType.Date).Value = endDateTime;
            }
            else if (selectedShift == "C")
            {
                DateTime startDateTime = selectedDate.Date.AddHours(22); // 10:00 PM
                DateTime endDateTime = selectedDate.Date.AddDays(1).AddHours(6); // 06:00 AM next day

                da.SelectCommand.Parameters.Add("Shift", OracleDbType.Varchar2).Value = selectedShift;
                da.SelectCommand.Parameters.Add("StartDateTime", OracleDbType.Date).Value = startDateTime;
                da.SelectCommand.Parameters.Add("EndDateTime", OracleDbType.Date).Value = endDateTime;
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
        MessageBox.Show("An error occurred: " + ex.Message);
    }
    finally
    {
        Db.ConClose();
    }

    dgvDelay.AutoGenerateColumns = false;
}