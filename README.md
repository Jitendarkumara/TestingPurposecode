public void LoadPdoRptGridSearch(DateTime searchDate, DateTime TosearchDate, string shift)
{
    try
    {
        Db.DatabaseConnect();

        DateTime startDateTime, endDateTime;

        switch (shift.ToUpper())
        {
            case "A":
                startDateTime = searchDate.Date.AddHours(6); // 06:00:00 on FromDate
                endDateTime = TosearchDate.Date.AddHours(14).AddSeconds(-1); // 13:59:59 on ToDate
                break;

            case "B":
                startDateTime = searchDate.Date.AddHours(14); // 14:00:00
                endDateTime = TosearchDate.Date.AddHours(22).AddSeconds(-1); // 21:59:59
                break;

            case "C":
                startDateTime = searchDate.Date.AddHours(22); // 22:00:00
                endDateTime = TosearchDate.AddDays(1).Date.AddHours(6).AddSeconds(-1); // Next day 05:59:59
                break;

            case "ALL":
                startDateTime = searchDate.Date;
                endDateTime = TosearchDate.Date.AddDays(1).AddSeconds(-1); // full to-date 23:59:59
                break;

            default:
                throw new Exception("Invalid shift selected.");
        }

        string formattedStart = startDateTime.ToString("yyyy-MM-dd HH:mm:ss");
        string formattedEnd = endDateTime.ToString("yyyy-MM-dd HH:mm:ss");

        string query = @"
            SELECT pdo.tcip_input_coil, 
                   pdo.tcip_product_coil,
                   pdo.tcpi_actual_wt, 
                   pdo.TCIP_CIL_END_TIME, 
                   pdo.L3_FLAG
            FROM t_col_coil_info_pdo pdo
            WHERE pdo.tcip_input_coil IN (
                SELECT l3.tc_coil_number
                FROM t_col_cot_pdi_l3 l3
                WHERE REGEXP_LIKE(l3.tc_id_message, '^\d{4}-\d{2}-\d{2}-\d{2}\.\d{2}\.\d{2}\.\d{6}$')
                  AND TO_TIMESTAMP(l3.tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') BETWEEN 
                      TO_TIMESTAMP(:startTime, 'YYYY-MM-DD HH24:MI:SS') AND 
                      TO_TIMESTAMP(:endTime, 'YYYY-MM-DD HH24:MI:SS')
                  AND l3.tc_coil_number NOT IN ('D1000', 'D2000')
            )
            ORDER BY pdo.TCIP_CIL_END_TIME DESC";

        OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
        da.SelectCommand.Parameters.Add(new OracleParameter(":startTime", formattedStart));
        da.SelectCommand.Parameters.Add(new OracleParameter(":endTime", formattedEnd));

        DtPDO = new DataTable();
        da.Fill(DtPDO);

        dataGridViewPDORPT.DataSource = DtPDO;
        dataGridViewPDORPT.AutoGenerateColumns = false;
        dataGridViewPDORPT.RowsDefaultCellStyle.BackColor = Color.WhiteSmoke;
        dataGridViewPDORPT.AlternatingRowsDefaultCellStyle.BackColor = Color.LightGray;

        Db.ConClose();
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error loading data: " + ex.Message);
    }
}