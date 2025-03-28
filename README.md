private void dataGridView1_CellContentClick(object sender, DataGridViewCellEventArgs e)
{
    // Ensure the clicked cell is a button and a valid row index
    if (e.RowIndex >= 0 && dataGridView1.Columns[e.ColumnIndex].Name == "EditSave")
    {
        DataGridViewRow row = dataGridView1.Rows[e.RowIndex];
        DataGridViewCell btnCell = row.Cells["EditSave"];

        if (btnCell.Value == null || btnCell.Value.ToString() == "Edit")
        {
            // Enable editing for Coil_ID
            row.Cells["Coil_ID"].ReadOnly = false;
            dataGridView1.BeginEdit(true); // Start edit mode
            btnCell.Value = "Save"; // Change button text dynamically
        }
        else if (btnCell.Value.ToString() == "Save")
        {
            try
            {
                // Get updated values
                string id = row.Cells["Tag"].Value?.ToString(); // Ensure null safety
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

                // Disable editing and change button text back to Edit
                row.Cells["Coil_ID"].ReadOnly = true;
                btnCell.Value = "Edit"; // Change button text back
                
                // Accept changes to reflect updated data
                ((DataTable)dataGridView1.DataSource).AcceptChanges();
                dataGridView1.Refresh(); // Refresh UI
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error updating record: " + ex.Message);
            }
        }
    }
}