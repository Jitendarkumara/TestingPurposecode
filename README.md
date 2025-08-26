public void ProcessEventTracking()
{
    try
    {
        Db.DatabaseConnect();

        string query = "SELECT ID_APP_TAG_EVENT, COIL_ID FROM T_EVENT_TRACKING";
        OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
        DataTable dtTrack = new DataTable();
        da.Fill(dtTrack);

        Db.ConClose();

        if (dtTrack.Rows.Count == 0) return;

        // 🔹 AUTO PROCESSING POR
        var uncRow = dtTrack.AsEnumerable()
            .FirstOrDefault(r => r["ID_APP_TAG_EVENT"].ToString() == "UNC_SELECTED");

        if (uncRow != null)
        {
            string coilId = uncRow["COIL_ID"].ToString();

            if (!string.IsNullOrEmpty(textBox17.Text))
            {
                if (coilId == textBox17.Text)
                {
                    btnPor1.BackColor = Color.Red;
                    btnPor1.Text = "Processing";
                    btnPor1End.Enabled = false;

                    btnPor2.BackColor = Color.FromArgb(59, 89, 152);
                    btnPor2.Enabled = false;
                    btnEnd.Enabled = false;

                    timerEntry.Start();
                    dat = DateTime.Now;
                }
                else
                {
                    btnPor1.BackColor = Color.FromArgb(59, 89, 152);
                    btnPor1.Text = "START";
                }
            }
            else if (!string.IsNullOrEmpty(textBox16.Text))
            {
                if (coilId == textBox16.Text)
                {
                    btnPor2.BackColor = Color.Red;
                    btnPor2.Text = "Processing";
                    btnEnd.Enabled = false;

                    btnPor1.BackColor = Color.FromArgb(59, 89, 152);
                    btnPor1.Enabled = false;
                    btnPor1End.Enabled = false;

                    Por2timer.Start();
                    timerEntry.Start();
                    dat = DateTime.Now;
                }
                else
                {
                    Por2timer.Stop();
                    btnPor2.BackColor = Color.FromArgb(59, 89, 152);
                    btnPor2.Text = "START";
                }
            }
        }

        // 🔹 RECOILER COIL
        var recoilRow = dtTrack.AsEnumerable()
            .FirstOrDefault(r => r["ID_APP_TAG_EVENT"].ToString() == "RECOILER");

        if (recoilRow != null && recoilRow["COIL_ID"] != DBNull.Value)
        {
            RecoilerSaddleFill();
        }
        else
        {
            txtRecoiller.Text = "";
        }

        // 🔹 AUTO POR1 COLOR PROCESSING
        if (btnPor1.Text == "Processing")
        {
            string por1Coil = txtPor1.Text;

            if (dtTrack.Rows.Count > 1 && dtTrack.Rows[1][1].ToString() == por1Coil)
            {
                diagonal1.BackLineColor = Color.Red;
                pict1.BackColor = Color.Red;
                diagonalSep1.backLineColorSep = Color.Red;
                pict3.BackColor = Color.Red;
                diagonal3.BackLineColor = Color.Red;
                pict5.BackColor = Color.Red;
                pict6.BackColor = Color.Red;
                diagonal4.BackLineColor = Color.Red;
                pict7.BackColor = Color.Red;
                diagonalSep2.backLineColorSep = Color.Red;
                pict8.BackColor = Color.Red;
                pict9.BackColor = Color.Red;
                pict10.BackColor = Color.Red;
                pict11.BackColor = Color.Red;
            }
            if (dtTrack.Rows.Count > 2 && dtTrack.Rows[2][1].ToString() == por1Coil)
            {
                ColorTllT0BR6();
            }
            if (dtTrack.Rows.Count > 3 && dtTrack.Rows[3][1].ToString() == por1Coil)
            {
                ColorBr6ToStr6();
            }
            if (dtTrack.Rows.Count > 4 && dtTrack.Rows[4][1].ToString() == por1Coil)
            {
                ColorStr6ToBr9();
                btnPor1End.Enabled = true;
            }
            if (dtTrack.Rows.Count > 5 && dtTrack.Rows[5][1].ToString() == por1Coil)
            {
                ColorBr9ToRecoil();
            }
            if (btnPor1.Text == "START")
            {
                PorColorBlack();
            }
        }

        // 🔹 AUTO POR2 COLOR PROCESSING
        if (btnPor2.Text == "Processing")
        {
            string por2Coil = txtPor2.Text;

            if (dtTrack.Rows.Count > 1 && dtTrack.Rows[1][1].ToString() == por2Coil)
            {
                pict3.BackColor = Color.Red;
                diagonal3.BackLineColor = Color.Red;
                pict5.BackColor = Color.Red;
                pict6.BackColor = Color.Red;
                diagonal4.BackLineColor = Color.Red;
                pict7.BackColor = Color.Red;
                diagonalSep2.backLineColorSep = Color.Red;
                pict8.BackColor = Color.Red;
                pict9.BackColor = Color.Red;
                pict10.BackColor = Color.Red;
                pict11.BackColor = Color.Red;
            }
            if (dtTrack.Rows.Count > 2 && dtTrack.Rows[2][1].ToString() == por2Coil)
            {
                ColorTllT0BR6();
            }
            if (dtTrack.Rows.Count > 3 && dtTrack.Rows[3][1].ToString() == por2Coil)
            {
                ColorBr6ToStr6();
            }
            if (dtTrack.Rows.Count > 4 && dtTrack.Rows[4][1].ToString() == por2Coil)
            {
                ColorStr6ToBr9();
                btnEnd.Enabled = true;
            }
            if (dtTrack.Rows.Count > 5 && dtTrack.Rows[5][1].ToString() == por2Coil)
            {
                ColorBr9ToRecoil();
            }
            if (btnPor2.Text == "START")
            {
                PorColorBlack();
            }
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show(ex.Message);
    }
}