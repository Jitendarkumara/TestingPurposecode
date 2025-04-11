 public void RecoilCoilId()
 {
     try
     {
         db.DatabaseConnect();
         // string s = "select COIL_ID from T_EVENT_TRACKING Where ID_APP_TAG_EVENT='L1_COIL_EXT' ";
         string s = "select COIL_ID from T_EVENT_TRACKING Where ID_APP_TAG_EVENT='RECOILER' ";
         OracleDataAdapter da = new OracleDataAdapter(s, conn);
         DataTable dtRecoile = new DataTable();
    
         da.Fill(dtRecoile);
         db.ConClose();
         if (dtRecoile.Rows.Count > 0)
         {
             if (dtRecoile.Rows[0][0] != DBNull.Value)
             {
                 txtRecoiler2.Text = dtRecoile.Rows[0][0].ToString();
             }
             else
             {
                 txtRecoiler2.Text = "";
             }
         }
      //   RecoilerSaddleFill();
     }
     catch (Exception ex)
     {
        // MessageBox.Show(ex.Message);
     }
 }
