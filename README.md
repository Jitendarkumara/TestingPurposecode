public void RecoilerSaddleFill()
{
    try
    { string Rec1_speed,Rec2_speed;
        Db.DatabaseConnect();
        // string s = "select COIL_ID from T_EVENT_TRACKING Where ID_APP_TAG_EVENT='L1_COIL_EXT' ";
        string s = "select TCIP_PRODUCT_COIL from T_COL_COIL_INFO_PDO ORDER BY  TCIP_CIL_END_TIME DESC FETCH FIRST 6 ROWS ONLY ";
        OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
        DataTable dtRecoile = new DataTable();
        da.Fill(dtRecoile);

        string Rec_Query = " SELECT RECOILER_1_SPEED_ACT, RECOILER_2_SPEED_ACT ,date_time FROM V_latest_Periodic_value_log";
        OracleDataAdapter Rec_da = new OracleDataAdapter(Rec_Query, Db.Con);
        DataTable Rec_dt = new DataTable();
        Rec_da.Fill(Rec_dt);
        Db.ConClose();
        if (Rec_dt.Rows.Count > 0)
        {
            Rec1_speed= Rec_dt.Rows[0]["RECOILER_1_SPEED_ACT"].ToString();
            Rec2_speed= Rec_dt.Rows[0]["RECOILER_2_SPEED_ACT"].ToString();
            //    if (dtRecoile.Rows[0][0] != DBNull.Value)
            //{
          
            textBox20.Text = dtRecoile.Rows[1]["TCIP_PRODUCT_COIL"].ToString();
            textBox23.Text = dtRecoile.Rows[2]["TCIP_PRODUCT_COIL"].ToString();
            textBox21.Text = dtRecoile.Rows[3]["TCIP_PRODUCT_COIL"].ToString();
            textBox24.Text = dtRecoile.Rows[4]["TCIP_PRODUCT_COIL"].ToString();
            textBox22.Text = dtRecoile.Rows[5]["TCIP_PRODUCT_COIL"].ToString();
            if (Convert.ToInt32(Rec1_speed) > 0)
            {
                Recoiler1_Timer.Start();
                    Recoiler2_Timer.Stop();

                textBoxRecoiler2.Text = dtRecoile.Rows[0]["TCIP_PRODUCT_COIL"].ToString();
                textBoxRecoiler1.Text = "";
            }
           else if (Convert.ToInt32(Rec2_speed) > 0)
            {
                textBoxRecoiler1.Text = dtRecoile.Rows[0]["TCIP_PRODUCT_COIL"].ToString();
                textBoxRecoiler2.Text = "";
                Recoiler2_Timer.Start();
                Recoiler1_Timer.Stop();
            }
            else
            {
                textBoxRecoiler1.Text = "";
                textBoxRecoiler2.Text = "";
            }
            //}
            //else
            //{
            //    txtRecoiller.Text = "";
            //}
        }
      
    }
    catch (Exception ex)
    {
        MessageBox.Show(ex.Message);
    }


}
