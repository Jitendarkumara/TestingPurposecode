private void LoadPdiRptGridSearch(DateTime selectedDate, DateTime TosearchDate, string shift)
{
    try
    {
        Db.DatabaseConnect();

        DateTime startDateTime, endDateTime;

        switch (shift.ToUpper())
        {
            case "A":
                startDateTime = selectedDate.Date.AddHours(6); // 06:00 AM on from-date
                endDateTime = TosearchDate.Date.AddHours(14).AddSeconds(-1); // 01:59:59 PM on to-date
                break;

            case "B":
                startDateTime = selectedDate.Date.AddHours(14); // 02:00 PM
                endDateTime = TosearchDate.Date.AddHours(22).AddSeconds(-1); // 09:59:59 PM
                break;

            case "C":
                startDateTime = selectedDate.Date.AddHours(22); // 10:00 PM
                endDateTime = TosearchDate.AddDays(1).Date.AddHours(6).AddSeconds(-1); // Next day 05:59:59 AM
                break;

            case "ALL":
                startDateTime = selectedDate.Date;
                endDateTime = TosearchDate.Date.AddDays(1).AddSeconds(-1); // Until 11:59:59 PM of to-date
                break;

            default:
                throw new Exception("Invalid shift selected.");
        }

        string formattedStart = startDateTime.ToString("yyyy-MM-dd HH:mm:ss");
        string formattedEnd = endDateTime.ToString("yyyy-MM-dd HH:mm:ss");

        string query = @"
            SELECT tc_coil_number, TC_WEIGHT, TC_ID_MESSAGE
            FROM t_col_cot_pdi_l3
            WHERE REGEXP_LIKE(tc_id_message, '^\d{4}-\d{2}-\d{2}-\d{2}\.\d{2}\.\d{2}\.\d{6}$')
              AND TO_TIMESTAMP(tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') BETWEEN
                  TO_TIMESTAMP(:startDate, 'YYYY-MM-DD HH24:MI:SS') AND
                  TO_TIMESTAMP(:endDate, 'YYYY-MM-DD HH24:MI:SS')
              AND tc_coil_number NOT IN ('D1000', 'D2000')
            ORDER BY TO_TIMESTAMP(tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') DESC";

        OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
        da.SelectCommand.Parameters.Add(new OracleParameter(":startDate", formattedStart));
        da.SelectCommand.Parameters.Add(new OracleParameter(":endDate", formattedEnd));

        DtPDI = new DataTable();
        da.Fill(DtPDI);

        dataGridViewRptPdi.DataSource = DtPDI;
        dataGridViewRptPdi.AutoGenerateColumns = true;
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error: " + ex.Message);
    }
}