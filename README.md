 private void Delay_Timer_Tick(object sender, EventArgs e)
 {
     Db.DatabaseConnect();
     string s = "SELECT L3_READ_FLAG,AGENCY_CODE,LINE_STOP_TIME,REMARKS FROM T_DELAY_SHEET_TEM  where REMARKS != 'R' and LINE_STOP_TIME> sysdate-7  order by LINE_STOP_TIME desc";
     OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
     DataTable dtDelay = new DataTable();
     da.Fill(dtDelay);
     Db.ConClose();
     if(dtDelay.Rows.Count>0)
     {
         MessageBox.Show("Kinldy Send delay data to MES");
     }
 }
