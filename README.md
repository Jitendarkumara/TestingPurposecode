// Put this on your Form (works for both Form and any Control)
private void SafeInvokeUI(Action action)
{
    if (this.InvokeRequired) this.Invoke(action);
    else action();
}

public void RecoilerSaddleFill()
{
    try
    {
        // --- queries
        const string coilQuery = @"
            SELECT TCIP_PRODUCT_COIL
            FROM T_COL_COIL_INFO_PDO
            ORDER BY TCIP_CIL_END_TIME DESC
            FETCH FIRST 6 ROWS ONLY";

        const string recQuery = @"
            SELECT RECOILER_1_SPEED_ACT, RECOILER_2_SPEED_ACT, date_time
            FROM V_latest_Periodic_value_log";

        // --- fetch data with fresh connections (NO Db.Con)
        DataTable dtRecoile = Db.ExecuteQuery(coilQuery);
        DataTable recDt     = Db.ExecuteQuery(recQuery);

        // --- parse speeds
        int rec1Speed = 0, rec2Speed = 0;
        if (recDt.Rows.Count > 0)
        {
            int.TryParse(Convert.ToString(recDt.Rows[0]["RECOILER_1_SPEED_ACT"]), out rec1Speed);
            int.TryParse(Convert.ToString(recDt.Rows[0]["RECOILER_2_SPEED_ACT"]), out rec2Speed);
        }

        // --- read coil IDs safely
        string c0 = dtRecoile.Rows.Count > 0 ? Convert.ToString(dtRecoile.Rows[0]["TCIP_PRODUCT_COIL"]) : "";
        string c1 = dtRecoile.Rows.Count > 1 ? Convert.ToString(dtRecoile.Rows[1]["TCIP_PRODUCT_COIL"]) : "";
        string c2 = dtRecoile.Rows.Count > 2 ? Convert.ToString(dtRecoile.Rows[2]["TCIP_PRODUCT_COIL"]) : "";
        string c3 = dtRecoile.Rows.Count > 3 ? Convert.ToString(dtRecoile.Rows[3]["TCIP_PRODUCT_COIL"]) : "";
        string c4 = dtRecoile.Rows.Count > 4 ? Convert.ToString(dtRecoile.Rows[4]["TCIP_PRODUCT_COIL"]) : "";
        string c5 = dtRecoile.Rows.Count > 5 ? Convert.ToString(dtRecoile.Rows[5]["TCIP_PRODUCT_COIL"]) : "";

        // --- update UI on UI thread (one Invoke)
        SafeInvokeUI(() =>
        {
            // last 5 coils
            textBox20.Text = c1;
            textBox23.Text = c2;
            textBox21.Text = c3;
            textBox24.Text = c4;
            textBox22.Text = c5;

            // timers + active coil display
            if (rec1Speed > 0)
            {
                textBoxRecoiler2.Text = c0;
                textBoxRecoiler1.Text = "";
                if (!Recoiler1_Timer.Enabled) Recoiler1_Timer.Start();
                if (Recoiler2_Timer.Enabled)  Recoiler2_Timer.Stop();
            }
            else if (rec2Speed > 0)
            {
                textBoxRecoiler1.Text = c0;
                textBoxRecoiler2.Text = "";
                if (!Recoiler2_Timer.Enabled) Recoiler2_Timer.Start();
                if (Recoiler1_Timer.Enabled)  Recoiler1_Timer.Stop();
            }
            else
            {
                textBoxRecoiler1.Text = "";
                textBoxRecoiler2.Text = "";
                if (Recoiler1_Timer.Enabled) Recoiler1_Timer.Stop();
                if (Recoiler2_Timer.Enabled) Recoiler2_Timer.Stop();
            }
        });
    }
    catch (Exception ex)
    {
        // avoid popup storms in timers; prefer a status label or log
        SafeInvokeUI(() =>
        {
            // MessageBox.Show("RecoilerSaddleFill error: " + ex.Message);
            statusLabel.Text = "RecoilerSaddleFill error: " + ex.Message; // use a label if you have one
        });
    }
}