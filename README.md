private bool isMessageShowing = false;

private void Delay_Timer_Tick(object sender, EventArgs e)
{
    Db.DatabaseConnect();
    string s = "SELECT L3_READ_FLAG,AGENCY_CODE,LINE_STOP_TIME,REMARKS " +
               "FROM T_DELAY_SHEET_TEM " +
               "WHERE REMARKS != 'R' AND LINE_STOP_TIME > sysdate-7 " +
               "ORDER BY LINE_STOP_TIME DESC";

    OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
    DataTable dtDelay = new DataTable();
    da.Fill(dtDelay);
    Db.ConClose();

    if (dtDelay.Rows.Count > 0 && !isMessageShowing)
    {
        isMessageShowing = true;
        MessageBox.Show("Kindly send delay data to MES");
        isMessageShowing = false;
    }
}