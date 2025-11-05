private void txtActualCoilWt_Leave(object sender, EventArgs e)
{
    string WtQuery = "select TC_WEIGHT from T_COL_COT_PDI_L3  where TC_COIL_NUMBER='" + txtMothCoilNum .Text+ "'";
    DataTable dtRecoile = Db.ExecuteQuery(WtQuery);
    int MaxWt = Convert.ToInt32(dtRecoile.Rows[0][0]);
    if (  Convert.ToInt32(txtActualCoilWt.Text)>10 || Convert.ToInt32(txtActualCoilWt.Text)>MaxWt)
    {
        MessageBox.Show("weight can not be greater than 10 or PDI weight");
    }
}
