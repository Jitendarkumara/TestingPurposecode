using ClosedXML.Excel;
using System.Data;
using System.Windows.Forms;

private void ExportToExcel(DataGridView dataGridView)
{
    // Convert DataGridView to DataTable
    DataTable dt = new DataTable();

    foreach (DataGridViewColumn column in dataGridView.Columns)
    {
        if (column.Visible)
            dt.Columns.Add(column.HeaderText);
    }

    foreach (DataGridViewRow row in dataGridView.Rows)
    {
        if (!row.IsNewRow)
        {
            DataRow dr = dt.NewRow();
            for (int i = 0; i < dataGridView.Columns.Count; i++)
            {
                if (dataGridView.Columns[i].Visible)
                    dr[i] = row.Cells[i].Value?.ToString() ?? "";
            }
            dt.Rows.Add(dr);
        }
    }

    using (SaveFileDialog sfd = new SaveFileDialog() { Filter = "Excel Workbook|*.xlsx" })
    {
        if (sfd.ShowDialog() == DialogResult.OK)
        {
            using (XLWorkbook wb = new XLWorkbook())
            {
                wb.Worksheets.Add(dt, "Sheet1");
                wb.SaveAs(sfd.FileName);
            }
            MessageBox.Show("Data Exported Successfully!", "Export", MessageBoxButtons.OK, MessageBoxIcon.Information);
        }
    }
}