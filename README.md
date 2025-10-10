private void dataGridView1_RowPrePaint(object sender, DataGridViewRowPrePaintEventArgs e)
{
    var row = dataGridView1.Rows[e.RowIndex];
    if (row.Cells["Flag"].Value != null && row.Cells["Flag"].Value.ToString().Trim().ToUpper() == "Y")
        row.DefaultCellStyle.BackColor = Color.LightGreen;
    else
        row.DefaultCellStyle.BackColor = Color.White;
}