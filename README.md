public WIP_Form wIP_Form; // Class-level variable

private void OpenWIPForm()
{
    // Close existing form if open
    if (wIP_Form != null && !wIP_Form.IsDisposed)
    {
        wIP_Form.Close();
        wIP_Form = null; // Set to null to avoid referencing a disposed object
    }

    // Ensure database connection is open
    Db.DatabaseConnect();
    
    string query = "SELECT TCIP_INPUT_COIL, TCIP_CIL_END_TIME FROM T_COL_COIL_INFO_PDO WHERE TCIP_PRODUCT_COIL='" + Lbl_coil.Text + "'";
    OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
    DataTable Dtcoil = new DataTable();
    da.Fill(Dtcoil);

    // Check if there is any data before proceeding
    if (Dtcoil.Rows.Count > 0)
    {
        DateTime endtime = (DateTime)Dtcoil.Rows[0]["TCIP_CIL_END_TIME"];
        string shift1;

        TimeSpan shift = endtime.TimeOfDay;
        if (shift >= new TimeSpan(6, 0, 0) && shift < new TimeSpan(14, 0, 0))
        {
            shift1 = "A";
        }
        else if (shift >= new TimeSpan(14, 0, 0) && shift < new TimeSpan(22, 0, 0))
        {
            shift1 = "B";
        }
        else
        {
            shift1 = "C";
        }

        // Store batch details
        BatchDetail.BatchNumber = Dtcoil.Rows[0]["TCIP_INPUT_COIL"].ToString();
        BatchDetail.shift = shift1;
        BatchDetail.Bdate = DateTime.Now.ToString();

        // Create and show the new form
        wIP_Form = new WIP_Form();
        wIP_Form.ShowDialog();
    }
    else
    {
        MessageBox.Show("No data found for the given coil number.", "Error", MessageBoxButtons.OK, MessageBoxIcon.Warning);
    }
}