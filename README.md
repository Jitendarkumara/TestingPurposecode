private void FillDelayGridShift(string selectedShift, DateTime selectedDate)
{
    string query;
    bool isAllShifts = selectedShift.Equals("All", StringComparison.OrdinalIgnoreCase);

    if (isAllShifts)
    {
        // If 'All' is selected, do not filter by shift
        query = "SELECT DELAY_ID, SHIFT, COIL_NUMBER, LINE_STOP_TIME, LINE_START_TIME, " +
                "DELAY_DURATION, REASON_CODE, OPERATOR_ID, REASON_DESCRIPTION, REMARKS, AGENCY_CODE, TOM " +
                "FROM T_DELAY_SHEET_TEM, T_SHIFT " +
                "WHERE T_DELAY_SHEET_TEM.SHIFT = T_SHIFT.SHIFT_NAME AND " +
                "TRUNC(LINE_START_TIME) = TRUNC(:SelectedDate) " +
                "ORDER BY LINE_STOP_TIME DESC";
    }
    else
    {
        // Otherwise, filter by selected shift
        query = "SELECT DELAY_ID, SHIFT, COIL_NUMBER, LINE_STOP_TIME, LINE_START_TIME, " +
                "DELAY_DURATION, REASON_CODE, OPERATOR_ID, REASON_DESCRIPTION, REMARKS, AGENCY_CODE, TOM " +
                "FROM T_DELAY_SHEET_TEM, T_SHIFT " +
                "WHERE T_DELAY_SHEET_TEM.SHIFT = T_SHIFT.SHIFT_NAME AND " +
                "T_DELAY_SHEET_TEM.SHIFT = :Shift AND TRUNC(LINE_START_TIME) = TRUNC(:SelectedDate) " +
                "ORDER BY LINE_STOP_TIME DESC";
    }

    try
    {
        Db.DatabaseConnect();

        using (OracleDataAdapter da = new OracleDataAdapter(query, Db.Con))
        {
            if (!isAllShifts)
            {
                da.SelectCommand.Parameters.Add("Shift", OracleDbType.Varchar2).Value = selectedShift;
            }

            da.SelectCommand.Parameters.Add("SelectedDate", OracleDbType.Date).Value = selectedDate.Date;

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