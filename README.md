private void dataGridView2_CellContentClick(object sender, DataGridViewCellEventArgs e)
{
    if (e.ColumnIndex == dataGridView2.Columns["Delete"].Index && e.RowIndex >= 0)
    {
        // Get the selected row's coil_id
        string coilId = dataGridView2.Rows[e.RowIndex].Cells["Coil_ID"].Value.ToString();

        // Ask for confirmation
        DialogResult result = MessageBox.Show($"Are you sure you want to delete Coil ID {coilId}?", 
            "Confirm Delete", MessageBoxButtons.OKCancel, MessageBoxIcon.Warning);

        if (result == DialogResult.OK)
        {
            try
            {
                // Delete record from database
                Db.DatabaseConnect();
                string deleteQuery = "DELETE FROM T_COIL_LOC_TEM WHERE coil_id = :coilId";
                using (OracleCommand cmd = new OracleCommand(deleteQuery, Db.Con))
                {
                    cmd.Parameters.Add(new OracleParameter("coilId", coilId));
                    cmd.ExecuteNonQuery();
                }
                Db.ConClose();

                // Remove row from DataGridView
                dataGridView2.Rows.RemoveAt(e.RowIndex);

                MessageBox.Show("Record deleted successfully!", "Success", MessageBoxButtons.OK, MessageBoxIcon.Information);
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error deleting data: " + ex.Message, "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
        }
    }
}