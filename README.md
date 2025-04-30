public void LoadRptGrid(DateTime selectedDate, DateTime TosearchDate, string shift)
{
    try
    {
        Db.DatabaseConnect();

        string formattedStart = selectedDate.ToString("yyyy-MM-dd HH:mm:ss");
        string formattedEnd = TosearchDate.ToString("yyyy-MM-dd HH:mm:ss");

        string baseQuery = @"
SELECT *
FROM (
    SELECT 
        PROD_SHIFT AS SHIFT,
        UPD_DATE AS UPDATE_DATE,
        COIL_NO,
        DECODE(CUR_STAGE, 'C', 'CCL', 'GL', 'GP/GL-2', 'G2', 'GP/GL-2', NULL) AS STAGE,
        DECODE(TXN_USER, 'LEVEL2', 'LEVEL2', 'MANUAL') AS STATUS  
    FROM DBPROD.PROD_WIP@DB_L3L2_GP02_PRD1
    WHERE 
        TRUNC(UPD_DATE) BETWEEN TRUNC(TO_DATE(:startDate, 'YYYY-MM-DD HH24:MI:SS')) 
                             AND TRUNC(TO_DATE(:endDate, 'YYYY-MM-DD HH24:MI:SS'))
        AND CUR_STAGE IN ('GL', 'G2')";

        // Conditionally add the shift filter
        if (shift != "All")
        {
            baseQuery += " AND PROD_SHIFT = :Shift";
        }

        baseQuery += @"
)
WHERE STATUS = 'MANUAL'";

        OracleDataAdapter da = new OracleDataAdapter(baseQuery, Db.Con);
        da.SelectCommand.Parameters.Add(new OracleParameter(":startDate", formattedStart));
        da.SelectCommand.Parameters.Add(new OracleParameter(":endDate", formattedEnd));

        if (shift != "All")
        {
            da.SelectCommand.Parameters.Add(new OracleParameter(":Shift", shift));
        }

        DataTable dt = new DataTable();
        da.Fill(dt);

        dataGridView1.AutoSizeColumnsMode = DataGridViewAutoSizeColumnsMode.AllCells;
        dataGridView1.DataSource = dt;
        dataGridView1.AutoGenerateColumns = true;

        Db.ConClose();

        dataGridView1.AutoGenerateColumns = false;
        dataGridView1.RowsDefaultCellStyle.BackColor = System.Drawing.Color.WhiteSmoke;
        dataGridView1.AlternatingRowsDefaultCellStyle.BackColor = System.Drawing.Color.LightGray;
    }
    catch (Exception ex)
    {
        MessageBox.Show(ex.Message);
    }
}