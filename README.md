public void RecoilerSaddleFill()
{
    try
    {
        // 🔹 Query last 6 coils
        string coilQuery = @"
            SELECT TCIP_PRODUCT_COIL 
            FROM T_COL_COIL_INFO_PDO 
            ORDER BY TCIP_CIL_END_TIME DESC 
            FETCH FIRST 6 ROWS ONLY";

        DataTable dtRecoile = Db.ExecuteQuery(coilQuery);

        // 🔹 Query recoiler speeds
        string recQuery = @"
            SELECT RECOILER_1_SPEED_ACT, RECOILER_2_SPEED_ACT, date_time 
            FROM V_latest_Periodic_value_log";

        DataTable recDt = Db.ExecuteQuery(recQuery);

        if (recDt.Rows.Count == 0 || dtRecoile.Rows.Count < 6)
        {
            textBoxRecoiler1.Text = "";
            textBoxRecoiler2.Text = "";
            return;
        }

        // 🔹 Parse speeds
        int rec1Speed = 0, rec2Speed = 0;
        int.TryParse(recDt.Rows[0]["RECOILER_1_SPEED_ACT"]?.ToString(), out rec1Speed);
        int.TryParse(recDt.Rows[0]["RECOILER_2_SPEED_ACT"]?.ToString(), out rec2Speed);

        // 🔹 Update last 5 coils in textboxes safely
        textBox20.Text = dtRecoile.Rows.Count > 1 ? dtRecoile.Rows[1]["TCIP_PRODUCT_COIL"].ToString() : "";
        textBox23.Text = dtRecoile.Rows.Count > 2 ? dtRecoile.Rows[2]["TCIP_PRODUCT_COIL"].ToString() : "";
        textBox21.Text = dtRecoile.Rows.Count > 3 ? dtRecoile.Rows[3]["TCIP_PRODUCT_COIL"].ToString() : "";
        textBox24.Text = dtRecoile.Rows.Count > 4 ? dtRecoile.Rows[4]["TCIP_PRODUCT_COIL"].ToString() : "";
        textBox22.Text = dtRecoile.Rows.Count > 5 ? dtRecoile.Rows[5]["TCIP_PRODUCT_COIL"].ToString() : "";

        // 🔹 Control recoiler timers & assign active coil
        if (rec1Speed > 0)
        {
            Recoiler1_Timer.Start();
            Recoiler2_Timer.Stop();

            textBoxRecoiler2.Text = dtRecoile.Rows[0]["TCIP_PRODUCT_COIL"].ToString();
            textBoxRecoiler1.Text = "";
        }
        else if (rec2Speed > 0)
        {
            Recoiler2_Timer.Start();
            Recoiler1_Timer.Stop();

            textBoxRecoiler1.Text = dtRecoile.Rows[0]["TCIP_PRODUCT_COIL"].ToString();
            textBoxRecoiler2.Text = "";
        }
        else
        {
            textBoxRecoiler1.Text = "";
            textBoxRecoiler2.Text = "";
            Recoiler1_Timer.Stop();
            Recoiler2_Timer.Stop();
        }
    }
    catch (Exception ex)
    {
        // ❌ Do not spam MessageBox inside Timer
        // Instead, log error and show once if needed
        Dbhelper.LogErrorToFile(ex.ToString(), "RecoilerSaddleFill");

        // Show only once for debugging:
        // MessageBox.Show("Error in RecoilerSaddleFill: " + ex.Message);
    }
}