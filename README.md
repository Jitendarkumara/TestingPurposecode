String sql = "SELECT L3_FLAG,TCIP_CIL_END_TIME FROM T_COL_COIL_INFO_PDO WHERE TCIP_PRODUCT_COIL ='" + txtPrdCoilNum.Text + "'";
dt = Db.ExecuteQuery(sql);
DateTime dat= DateTime.Now;
if ((dat - Convert.ToDateTime(dt.Rows[0][1])) > 12)
{
    // If the L3_FLAG is not 'Y', proceed with the transfer
    TrasferToMES();
}
else
{
    MessageBox.Show("12 hour has crossed GR not possible for this coil");
}
