 Db.DatabaseConnect();
 // string s = " SELECT CGD_ID_COIL FROM T_PDO_INFO order by CGD_TS_END DESC";
 string s = "SELECT CGD_ID_COIL COIL_ID , to_char(CGD_TS_END,'DD-MON-YY HH.MI.SS AM') END_TIME FROM T_PDO_INFO order by CGD_TS_END DESC";
 OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
 DataTable dt = new DataTable();
 da.Fill(dt);
 dgvPDO.DataSource = dt;
 //dgvPDO.AutoGenerateColumns = false;

 dgvPDO.RowsDefaultCellStyle.BackColor = Color.WhiteSmoke;
 dgvPDO.AlternatingRowsDefaultCellStyle.BackColor = Color.LightGray;
 Db.ConClose();
            
