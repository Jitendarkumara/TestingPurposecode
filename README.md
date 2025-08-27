public void FetchAndUpdateUI()
{
    try
    {
        string query = @"
            SELECT ENTRY_ACCUMULATOR_POS_PERCENT,
                   EXIT_ACCUMULATOR_POS_PERCENT,
                   PROCESS_LINE_SPEED_ACT,
                   CHEM_DRYER_TEMP_ACT,
                   FIN_OVN_TEMP_Z1_ACT, FIN_OVN_TEMP_Z2_ACT, FIN_OVN_TEMP_Z3_ACT, FIN_OVN_TEMP_Z4_ACT,
                   PRIME_OVN_TEMP_Z1_ACT, PRIME_OVN_TEMP_Z2_ACT, PRIME_OVN_TEMP_Z3_ACT,
                   TLL_TENSION_ACT, BRUSH_TANK_TEMP, BRUSH_TANK_CONDUCTIVITY,
                   ALKALI_TANK_WATER_LVL,
                   HOT_AIR_DRY_1_TEMP_SCL, HOT_AIR_DRY_2_TEMP_SCL, HOT_AIR_DRY_3_TEMP_SCL
            FROM V_LATEST_PERIODIC_VALUE";

        DataTable dt = Db.ExecuteQuery(query);
        if (dt.Rows.Count == 0) return;

        DataRow row = dt.Rows[0];

        // --- Safely update UI controls
        SafeInvoke(textBoxChemDryTemp, () => textBoxChemDryTemp.Text = row["CHEM_DRYER_TEMP_ACT"]?.ToString());
        SafeInvoke(textBoxFOZ1, () => textBoxFOZ1.Text = row["FIN_OVN_TEMP_Z1_ACT"]?.ToString());
        SafeInvoke(textBoxFOZ2, () => textBoxFOZ2.Text = row["FIN_OVN_TEMP_Z2_ACT"]?.ToString());
        SafeInvoke(textBoxFOZ3, () => textBoxFOZ3.Text = row["FIN_OVN_TEMP_Z3_ACT"]?.ToString());
        SafeInvoke(textBoxFOZ4, () => textBoxFOZ4.Text = row["FIN_OVN_TEMP_Z4_ACT"]?.ToString());
        SafeInvoke(textBoxPOZ1, () => textBoxPOZ1.Text = row["PRIME_OVN_TEMP_Z1_ACT"]?.ToString());
        SafeInvoke(textBoxPOZ2, () => textBoxPOZ2.Text = row["PRIME_OVN_TEMP_Z2_ACT"]?.ToString());
        SafeInvoke(textBoxPOZ3, () => textBoxPOZ3.Text = row["PRIME_OVN_TEMP_Z3_ACT"]?.ToString());
        SafeInvoke(textBoxTLLTEN, () => textBoxTLLTEN.Text = row["TLL_TENSION_ACT"]?.ToString());
        SafeInvoke(textBoxBrushTankTemp, () => textBoxBrushTankTemp.Text = row["BRUSH_TANK_TEMP"]?.ToString());
        SafeInvoke(textBoxBrushTankCond, () => textBoxBrushTankCond.Text = row["BRUSH_TANK_CONDUCTIVITY"]?.ToString());
        SafeInvoke(textBoxAlkaliLevel, () => textBoxAlkaliLevel.Text = row["ALKALI_TANK_WATER_LVL"]?.ToString());
        SafeInvoke(textBoxblower1, () => textBoxblower1.Text = row["HOT_AIR_DRY_1_TEMP_SCL"]?.ToString());
        SafeInvoke(textBoxblower2, () => textBoxblower2.Text = row["HOT_AIR_DRY_2_TEMP_SCL"]?.ToString());
        SafeInvoke(textBoxblower3, () => textBoxblower3.Text = row["HOT_AIR_DRY_3_TEMP_SCL"]?.ToString());

        // --- Entry Accumulator
        int entryPercent = row["ENTRY_ACCUMULATOR_POS_PERCENT"] == DBNull.Value ? 0 : Convert.ToInt32(row["ENTRY_ACCUMULATOR_POS_PERCENT"]);
        entryPercent = Math.Max(0, Math.Min(entryPercent, 100));
        SafeInvoke(progressBarEntry, () => progressBarEntry.Value = entryPercent);
        SafeInvoke(label72, () => label72.Text = entryPercent + "%");
        SafeInvoke(pictureBox45, () => pictureBox45.Height = 200 - entryPercent * 2);

        // --- Exit Accumulator
        int exitPercent = row["EXIT_ACCUMULATOR_POS_PERCENT"] == DBNull.Value ? 0 : Convert.ToInt32(row["EXIT_ACCUMULATOR_POS_PERCENT"]);
        exitPercent = Math.Max(0, Math.Min(exitPercent, 100));
        SafeInvoke(progressBarExit, () => progressBarExit.Value = exitPercent);
        SafeInvoke(label73, () => label73.Text = exitPercent + "%");
        SafeInvoke(pictureBox47, () => pictureBox47.Height = 200 - exitPercent * 2);

        // --- Mill Speed
        string speedStr = row["PROCESS_LINE_SPEED_ACT"]?.ToString();
        SafeInvoke(txtSpeed, () => txtSpeed.Text = speedStr);

        if (speedStr == "0")
        {
            Por1timer.Stop();
            Por2timer.Stop();
            Recoiler1_Timer.Stop();
            Recoiler2_Timer.Stop();
        }
        else
        {
            string uncoilerQuery = "SELECT * FROM T_UNCOILER_SEL ORDER BY WRITE_TIMESTAMP DESC FETCH FIRST 1 ROW ONLY";
            DataTable dtUncoiler = Db.ExecuteQuery(uncoilerQuery);

            if (dtUncoiler.Rows.Count > 0)
            {
                if (dtUncoiler.Rows[0][0].ToString() == "UNC_1_SELECTED")
                {
                    if (!Por1timer.Enabled) Por1timer.Start();
                    if (Por2timer.Enabled) Por2timer.Stop();
                }
                else
                {
                    if (!Por2timer.Enabled) Por2timer.Start();
                    if (Por1timer.Enabled) Por1timer.Stop();
                }
            }
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error in FetchAndUpdateUI: " + ex.Message);
    }
}