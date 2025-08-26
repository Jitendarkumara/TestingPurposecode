public void FetchAndUpdateUI()
{
    try
    {
        Db.DatabaseConnect();

        if (Db.Con == null || Db.Con.State != ConnectionState.Open)
            throw new Exception("Database connection is not available.");

        string query = @"SELECT ENTRY_ACCUMULATOR_POS_PERCENT,
                                EXIT_ACCUMULATOR_POS_PERCENT,
                                PROCESS_LINE_SPEED_ACT,
                                CHEM_DRYER_TEMP_ACT,
                                FIN_OVN_TEMP_Z1_ACT, FIN_OVN_TEMP_Z2_ACT, FIN_OVN_TEMP_Z3_ACT, FIN_OVN_TEMP_Z4_ACT,
                                PRIME_OVN_TEMP_Z1_ACT, PRIME_OVN_TEMP_Z2_ACT, PRIME_OVN_TEMP_Z3_ACT,
                                TLL_TENSION_ACT, BRUSH_TANK_TEMP, BRUSH_TANK_CONDUCTIVITY,
                                ALKALI_TANK_WATER_LVL,
                                HOT_AIR_DRY_1_TEMP_SCL, HOT_AIR_DRY_2_TEMP_SCL, HOT_AIR_DRY_3_TEMP_SCL
                         FROM V_LATEST_PERIODIC_VALUE";

        DataTable dt = new DataTable();
        using (OracleDataAdapter da = new OracleDataAdapter(query, Db.Con))
        {
            da.Fill(dt);
        }

        if (dt.Rows.Count == 0)
        {
            MessageBox.Show("⚠️ No data found in V_LATEST_PERIODIC_VALUE.");
            return;
        }

        DataRow row = dt.Rows[0];

        // --- Update TextBoxes (Temperature, Tension, etc.)
        textBoxChemDryTemp.Text    = row["CHEM_DRYER_TEMP_ACT"].ToString();
        textBoxFOZ1.Text           = row["FIN_OVN_TEMP_Z1_ACT"].ToString();
        textBoxFOZ2.Text           = row["FIN_OVN_TEMP_Z2_ACT"].ToString();
        textBoxFOZ3.Text           = row["FIN_OVN_TEMP_Z3_ACT"].ToString();
        textBoxFOZ4.Text           = row["FIN_OVN_TEMP_Z4_ACT"].ToString();
        textBoxPOZ1.Text           = row["PRIME_OVN_TEMP_Z1_ACT"].ToString();
        textBoxPOZ2.Text           = row["PRIME_OVN_TEMP_Z2_ACT"].ToString();
        textBoxPOZ3.Text           = row["PRIME_OVN_TEMP_Z3_ACT"].ToString();
        textBoxTLLTEN.Text         = row["TLL_TENSION_ACT"].ToString();
        textBoxBrushTankTemp.Text  = row["BRUSH_TANK_TEMP"].ToString();
        textBoxBrushTankCond.Text  = row["BRUSH_TANK_CONDUCTIVITY"].ToString();
        textBoxAlkaliLevel.Text    = row["ALKALI_TANK_WATER_LVL"].ToString();
        textBoxblower1.Text        = row["HOT_AIR_DRY_1_TEMP_SCL"].ToString();
        textBoxblower2.Text        = row["HOT_AIR_DRY_2_TEMP_SCL"].ToString();
        textBoxblower3.Text        = row["HOT_AIR_DRY_3_TEMP_SCL"].ToString();

        // --- Update Entry Accumulator ProgressBar
        int entryPercent = Convert.ToInt32(row["ENTRY_ACCUMULATOR_POS_PERCENT"]);
        progressBarEntry.Value = Math.Min(entryPercent, 100);
        label72.Text = progressBarEntry.Value + "%";
        pictureBox45.Height = 200 - progressBarEntry.Value * 2;

        // --- Update Exit Accumulator ProgressBar
        int exitPercent = Convert.ToInt32(row["EXIT_ACCUMULATOR_POS_PERCENT"]);
        progressBarExit.Value = Math.Min(exitPercent, 100);
        label73.Text = progressBarExit.Value + "%";
        pictureBox47.Height = 200 - progressBarExit.Value * 2;

        // --- Update Mill Speed and Timers
        string speedStr = row["PROCESS_LINE_SPEED_ACT"].ToString();
        txtSpeed.Text = speedStr;

        if (speedStr == "0")
        {
            Por1timer.Stop();
            Por2timer.Stop();
            Recoiler1_Timer.Stop();
            Recoiler2_Timer.Stop();
        }
        else
        {
            // fetch uncoupler selector (only when running)
            string uncoilerQuery = "SELECT * FROM T_UNCOILER_SEL ORDER BY WRITE_TIMESTAMP DESC FETCH FIRST 1 ROW ONLY";
            DataTable dtUncoiler = new DataTable();
            using (OracleDataAdapter daUnc = new OracleDataAdapter(uncoilerQuery, Db.Con))
            {
                daUnc.Fill(dtUncoiler);
            }

            if (dtUncoiler.Rows.Count > 0)
            {
                if (dtUncoiler.Rows[0][0].ToString() == "UNC_1_SELECTED")
                {
                    Por1timer.Start();
                    Por2timer.Stop();
                }
                else
                {
                    Por2timer.Start();
                    Por1timer.Stop();
                }
            }
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error in FetchAndUpdateUI: " + ex.Message);
    }
    finally
    {
        Db.ConClose();
    }
}