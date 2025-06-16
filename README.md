private void FillDelayGridShift(string selectedShift, DateTime selectedDate)
{
    string query;
    bool isAllShifts = selectedShift.Equals("All", StringComparison.OrdinalIgnoreCase);

    if (isAllShifts)
    {
        query = @"SELECT DELAY_ID, SHIFT, COIL_NUMBER, LINE_STOP_TIME, LINE_START_TIME,
                         DELAY_DURATION, REASON_CODE, OPERATOR_ID, REASON_DESCRIPTION, REMARKS, AGENCY_CODE, TOM
                  FROM T_DELAY_SHEET_TEM
                  WHERE LINE_START_TIME BETWEEN TO_DATE(:StartDateTime, 'DD-MON-YY HH24:MI:SS') 
                                           AND TO_DATE(:EndDateTime, 'DD-MON-YY HH24:MI:SS')
                  ORDER BY LINE_STOP_TIME";
    }
    else if (selectedShift == "C")
    {
        query = @"SELECT DELAY_ID, SHIFT, COIL_NUMBER, LINE_STOP_TIME, LINE_START_TIME,
                         DELAY_DURATION, REASON_CODE, OPERATOR_ID, REASON_DESCRIPTION, REMARKS, AGENCY_CODE, TOM
                  FROM T_DELAY_SHEET_TEM
                  WHERE SHIFT = :Shift AND
                        LINE_START_TIME BETWEEN TO_DATE(:StartDateTime, 'DD-MON-YY HH24:MI:SS') 
                                             AND TO_DATE(:EndDateTime, 'DD-MON-YY HH24:MI:SS')
                  ORDER BY LINE_STOP_TIME";
    }
    else
    {
        query = @"SELECT DELAY_ID, SHIFT, COIL_NUMBER, LINE_STOP_TIME, LINE_START_TIME,
                         DELAY_DURATION, REASON_CODE, OPERATOR_ID, REASON_DESCRIPTION, REMARKS, AGENCY_CODE, TOM
                  FROM T_DELAY_SHEET_TEM
                  WHERE SHIFT = :Shift AND 
                        TRUNC(LINE_START_TIME) = TRUNC(TO_DATE(:SelectedDate, 'DD-MON-YY'))
                  ORDER BY LINE_STOP_TIME";
    }

    try
    {
        Db.DatabaseConnect();

        using (OracleDataAdapter da = new OracleDataAdapter(query, Db.Con))
        {
            if (isAllShifts)
            {
                string startDateTime = selectedDate.ToString("dd-MMM-yy", CultureInfo.InvariantCulture).ToUpper() + " 06:00:00";
                string endDateTime = selectedDate.AddDays(1).ToString("dd-MMM-yy", CultureInfo.InvariantCulture).ToUpper() + " 06:00:00";
                da.SelectCommand.Parameters.Add("StartDateTime", OracleDbType.Varchar2).Value = startDateTime;
                da.SelectCommand.Parameters.Add("EndDateTime", OracleDbType.Varchar2).Value = endDateTime;
            }
            else if (selectedShift == "C")
            {
                string startDateTime = selectedDate.ToString("dd-MMM-yy", CultureInfo.InvariantCulture).ToUpper() + " 22:00:00";
                string endDateTime = selectedDate.AddDays(1).ToString("dd-MMM-yy", CultureInfo.InvariantCulture).ToUpper() + " 06:00:00";
                da.SelectCommand.Parameters.Add("Shift", OracleDbType.Varchar2).Value = selectedShift;
                da.SelectCommand.Parameters.Add("StartDateTime", OracleDbType.Varchar2).Value = startDateTime;
                da.SelectCommand.Parameters.Add("EndDateTime", OracleDbType.Varchar2).Value = endDateTime;
            }
            else
            {
                string selectedDateStr = selectedDate.ToString("dd-MMM-yy", CultureInfo.InvariantCulture).ToUpper();
                da.SelectCommand.Parameters.Add("Shift", OracleDbType.Varchar2).Value = selectedShift;
                da.SelectCommand.Parameters.Add("SelectedDate", OracleDbType.Varchar2).Value = selectedDateStr;
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