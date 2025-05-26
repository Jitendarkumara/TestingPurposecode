   try
   {
       
       Db.DatabaseConnect();
       string s = "SELECT  TCIP_PRODUCT_COIL Coil_NUM,TO_CHAR(TCIP_CIL_END_TIME, 'DD-MON-YY HH.MI.SS AM') AS Date_Time  FROM T_COL_COIL_INFO_PDO  WHERE TRUNC(TO_TIMESTAMP(TCIP_CIL_END_TIME, 'DD-MON-RR HH12.MI.SS.FF9 PM')) > TRUNC(SYSDATE-30)  ORDER BY TCIP_CIL_END_TIME DESC";

      // string s = "SELECT TCIP_PRODUCT_COIL, TCIP_CIL_END_TIME  FROM T_COL_COIL_INFO_PDO  WHERE TRUNC(TO_TIMESTAMP(TCIP_CIL_END_TIME, 'DD-MON-RR HH12.MI.SS.FF9 PM')) > TRUNC(SYSDATE-30)  ORDER BY TO_TIMESTAMP(TCIP_CIL_END_TIME, 'DD-MON-RR HH12.MI.SS.FF9 PM') DESC";
       OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
       DtPDO = new DataTable();
       da.Fill(DtPDO);
     
       dgvPDO.DataSource = DtPDO;
       dgvPDO.AutoGenerateColumns = false;
       // here we are checking new coil if new coil generate then sticker will Auto dissplay
       if (Lbl_coil.Text == "Coil")
       {
           Lbl_coil.Text = DtPDO.Rows[0][0].ToString();
           //printSticker();
       }
       else
       {
           if (Lbl_coil.Text != DtPDO.Rows[0][0].ToString())
           {
               Lbl_coil.Text = DtPDO.Rows[0][0].ToString();
               printSticker();
             
           }
          
       }

       Db.ConClose();
       //dgvCoilDetails.RowHeadersDefaultCellStyle.SelectionBackColor = Color.Red;
       dgvPDO.RowHeadersWidth = 5;
       dgvPDO.AutoSizeColumnsMode = DataGridViewAutoSizeColumnsMode.AllCells;
  
       dgvPDO.AutoResizeColumns(DataGridViewAutoSizeColumnsMode.AllCells);
       dgvPDO.RowsDefaultCellStyle.BackColor = Color.WhiteSmoke;
       dgvPDO.AlternatingRowsDefaultCellStyle.BackColor = Color.LightGray;
   }
   catch (Exception ex)
   {
       MessageBox.Show(ex.Message);
   }
