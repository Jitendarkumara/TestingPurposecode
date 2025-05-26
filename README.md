string newCoilNum = DtPDO.Rows.Count > 0 ? DtPDO.Rows[0][0].ToString() : "";

if (dgvPDO.Rows.Count == 0 || dgvPDO.Rows[0].Cells[0].Value.ToString() != newCoilNum)
{
    dgvPDO.DataSource = DtPDO;
}