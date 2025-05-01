try
{
    porselected += 1;
    if (btnPOR2.BackColor != Color.Maroon)
    {
        if (dataGridViewLine.Rows[0].Cells[4].Value != null)
        {
            gridBtnTxt = dataGridViewLine.Rows[0].Cells[4].Value.ToString();
        }
        tempTracking = dtToLine.Copy();
        dataGridViewLine.DataSource = tempTracking;
        if (dataGridView1.SelectedRows.Count > 0)
        {
            DataGridViewRow row = dataGridView1.SelectedRows[0];
            DataRow dr = tempTracking.NewRow();
            dr[0] = "L1_POR2_SEL";
            dr[1] = row.Cells[0].Value;
            dr[2] = row.Cells[1].Value;
            dr[3] = row.Cells[2].Value;
            tempTracking.Rows.RemoveAt(4);
            tempTracking.Rows.InsertAt(dr, 4);
            lstsdlCoil.Add(dr[1].ToString());
            btnPOR2.BackColor = Color.Maroon;
            int LineRowIndex = lstsdlCoil.Count - 1;
            string selectedCoil = row.Cells[0].Value.ToString();
            ReorderPDIgrid(selectedCoil, LineRowIndex);
            dataGridViewLine.Rows[4].Cells[4].Value = "Cancel";
            dataGridViewLine.Rows[0].Cells[4].Value = gridBtnTxt;
            SetLineGridColor();
            buttonExecute.Enabled = true;
            dtToLine = tempTracking.Copy();
        }
    }
    
}
catch (Exception ex)
{
    MessageBox.Show(ex.Message);
}
