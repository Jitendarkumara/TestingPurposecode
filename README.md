private void txtActualCoilWt_Leave(object sender, EventArgs e)
{
    // Validate user input first
    if (!int.TryParse(txtActualCoilWt.Text, out int actualWt))
    {
        MessageBox.Show("Please enter a valid weight value.");
        txtActualCoilWt.Focus();
        return;
    }

    // Fetch PDI weight from database
    string query = $"SELECT TC_WEIGHT FROM T_COL_COT_PDI_L3 WHERE TC_COIL_NUMBER = '{txtMothCoilNum.Text}'";
    DataTable dt = Db.ExecuteQuery(query);

    if (dt.Rows.Count == 0)
    {
        MessageBox.Show("No record found for the given coil number.");
        return;
    }

    int maxWt = Convert.ToInt32(dt.Rows[0]["TC_WEIGHT"]);

    // Validate against limits
    if (actualWt > 10 || actualWt > maxWt)
    {
        MessageBox.Show("Weight cannot be greater than 10 or PDI weight.");
        txtActualCoilWt.Focus();
    }
}