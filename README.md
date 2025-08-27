// ✅ Helper for safe UI updates from any thread
private void SafeInvoke(Control ctrl, Action action)
{
    if (ctrl.InvokeRequired)
        ctrl.Invoke(action);
    else
        action();
}

public void RecoilerSaddleFill()
{
    try
    {
        Db.DatabaseConnect();

        // 🔹 Query last 6 coils
        string coilQuery = @"SELECT TCIP_PRODUCT_COIL 
                             FROM T_COL_COIL_INFO_PDO 
                             ORDER BY TCIP_CIL_END_TIME DESC 
                             FETCH FIRST 6 ROWS ONLY";

        DataTable dtRecoile = new DataTable();
        using (OracleDataAdapter da = new OracleDataAdapter(coilQuery, Db.Con))
        {
            da.Fill(dtRecoile);
        }

        // 🔹 Query recoiler speeds
        string recQuery = @"SELECT RECOILER_1_SPEED_ACT, RECOILER_2_SPEED_ACT, date_time 
                            FROM V_latest_Periodic_value_log";

        DataTable recDt = new DataTable();
        using (OracleDataAdapter rec_da = new OracleDataAdapter(recQuery, Db.Con))
        {
            rec_da.Fill(recDt);
        }

        Db.ConClose();

        if (recDt.Rows.Count == 0 || dtRecoile.Rows.Count < 6)
        {
            // No valid data → clear UI safely
            SafeInvoke(textBoxRecoiler1, () => textBoxRecoiler1.Text = "");
            SafeInvoke(textBoxRecoiler2, () => textBoxRecoiler2.Text = "");
            return;
        }

        // 🔹 Safe speed parsing
        int rec1Speed = 0, rec2Speed = 0;
        int.TryParse(recDt.Rows[0]["RECOILER_1_SPEED_ACT"]?.ToString(), out rec1Speed);
        int.TryParse(recDt.Rows[0]["RECOILER_2_SPEED_ACT"]?.ToString(), out rec2Speed);

        // 🔹 Update last 5 coils in textboxes safely
        SafeInvoke(textBox20, () => textBox20.Text = dtRecoile.Rows.Count > 1 ? dtRecoile.Rows[1]["TCIP_PRODUCT_COIL"].ToString() : "");
        SafeInvoke(textBox23, () => textBox23.Text = dtRecoile.Rows.Count > 2 ? dtRecoile.Rows[2]["TCIP_PRODUCT_COIL"].ToString() : "");
        SafeInvoke(textBox21, () => textBox21.Text = dtRecoile.Rows.Count > 3 ? dtRecoile.Rows[3]["TCIP_PRODUCT_COIL"].ToString() : "");
        SafeInvoke(textBox24, () => textBox24.Text = dtRecoile.Rows.Count > 4 ? dtRecoile.Rows[4]["TCIP_PRODUCT_COIL"].ToString() : "");
        SafeInvoke(textBox22, () => textBox22.Text = dtRecoile.Rows.Count > 5 ? dtRecoile.Rows[5]["TCIP_PRODUCT_COIL"].ToString() : "");

        // 🔹 Control recoiler timers & assign active coil safely
        if (rec1Speed > 0)
        {
            SafeInvoke(textBoxRecoiler2, () => textBoxRecoiler2.Text = dtRecoile.Rows[0]["TCIP_PRODUCT_COIL"].ToString());
            SafeInvoke(textBoxRecoiler1, () => textBoxRecoiler1.Text = "");

            SafeInvoke(Recoiler1_Timer, () => Recoiler1_Timer.Start());
            SafeInvoke(Recoiler2_Timer, () => Recoiler2_Timer.Stop());
        }
        else if (rec2Speed > 0)
        {
            SafeInvoke(textBoxRecoiler1, () => textBoxRecoiler1.Text = dtRecoile.Rows[0]["TCIP_PRODUCT_COIL"].ToString());
            SafeInvoke(textBoxRecoiler2, () => textBoxRecoiler2.Text = "");

            SafeInvoke(Recoiler2_Timer, () => Recoiler2_Timer.Start());
            SafeInvoke(Recoiler1_Timer, () => Recoiler1_Timer.Stop());
        }
        else
        {
            SafeInvoke(textBoxRecoiler1, () => textBoxRecoiler1.Text = "");
            SafeInvoke(textBoxRecoiler2, () => textBoxRecoiler2.Text = "");

            SafeInvoke(Recoiler1_Timer, () => Recoiler1_Timer.Stop());
            SafeInvoke(Recoiler2_Timer, () => Recoiler2_Timer.Stop());
        }
    }
    catch (Exception ex)
    {
        Db.ConClose(); // ensure connection is closed on error
        SafeInvoke(this, () => MessageBox.Show("Error in RecoilerSaddleFill: " + ex.Message));
    }
}