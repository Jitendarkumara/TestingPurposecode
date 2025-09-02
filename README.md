string sql = "SELECT L3_FLAG, TCIP_CIL_END_TIME 
              FROM T_COL_COIL_INFO_PDO 
              WHERE TCIP_PRODUCT_COIL ='" + txtPrdCoilNum.Text + "'";

DataTable dt = Db.ExecuteQuery(sql);

if (dt.Rows.Count > 0)
{
    DateTime dat = DateTime.Now;
    DateTime coilEndTime = Convert.ToDateTime(dt.Rows[0]["TCIP_CIL_END_TIME"]);

    if ((dat - coilEndTime) > TimeSpan.FromHours(12))
    {
        // If the L3_FLAG is not 'Y', proceed with the transfer
        if (dt.Rows[0]["L3_FLAG"].ToString() != "Y")
        {
            TrasferToMES();
        }
        else
        {
            MessageBox.Show("L3_FLAG is 'Y', transfer not allowed.");
        }
    }
    else
    {
        MessageBox.Show("12 hours have not yet passed. GR not possible for this coil.");
    }
}
else
{
    MessageBox.Show("No data found for the given coil number.");
}