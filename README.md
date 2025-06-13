if (dgvPDO.Rows.Count == 0 || dgvPDO.Rows[0].Cells[0].Value == null || dgvPDO.Rows[0].Cells[0].Value.ToString() != newCoilNum)
{
    dgvPDO.DataSource = DtPDO;
}