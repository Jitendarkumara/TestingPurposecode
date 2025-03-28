private void dataGridView1_CellContentClick(object sender, DataGridViewCellEventArgs e)
{
    if (e.RowIndex >= 0 && dataGridView1.Columns[e.ColumnIndex].Name == "EditSave")
    {
        DataGridViewRow row = dataGridView1.Rows[e.RowIndex];
        DataGridViewCell btnCell = row.Cells["EditSave"];

        if (btnCell.Value == null || btnCell.Value.ToString() == "Edit")
        {
            // Enable editing for Coil_ID
            row.Cells["Coil_ID"].ReadOnly = false;

            // Set focus on the Coil_ID cell
            dataGridView1.CurrentCell = row.Cells["Coil_ID"];
            dataGridView1.BeginEdit(true); // Start editing mode

            btnCell.Value = "Save"; // Change button text dynamically
            dataGridView1.RefreshEdit(); // Refresh the button column
        }
        else if (btnCell.Value.ToString() == "Save")
        {
            try
            {
                // Get updated values
                string id = row.Cells["Tag"].Value?.ToString();
                string coilId = row.Cells["Coil_ID"].Value?.ToString();

                if (string.IsNullOrEmpty(id) || string.IsNullOrEmpty(coilId))
                {
                    MessageBox.Show("Tag or Coil_ID cannot be empty.");
                    return;
                }

                // Update query
                string updateQuery = "UPDATE T_Event_Tracking SET Coil_id = :CoilID WHERE Id_app_tag_Event = :Id";

                Db.DatabaseConnect();
                using (OracleCommand cmd = new OracleCommand(updateQuery, Db.Con))
                {
                    cmd.Parameters.Add(new OracleParameter(":CoilID", coilId));
                    cmd.Parameters.Add(new OracleParameter(":Id", id));

                    int rowsAffected = cmd.ExecuteNonQuery();
                    if (rowsAffected > 0)
                    {
                        MessageBox.Show("Record updated successfully.");
                    }
                    else
                    {
                        MessageBox.Show("No record updated. Check if the ID exists.");
                    }
                }
                Db.ConClose();

                // Disable editing and reset button text
                row.Cells["Coil_ID"].ReadOnly = true;
                btnCell.Value = "Edit"; // Explicitly set button text

                // Force UI refresh to reflect the button text update
                dataGridView1.RefreshEdit(); // Refresh the button column
                dataGridView1.Invalidate(); // Force UI redraw

                // Ensure changes are committed to the DataTable
                if (dataGridView1.DataSource is DataTable dt)
                {
                    dt.AcceptChanges();
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error updating record: " + ex.Message);
            }
        }
    }
}