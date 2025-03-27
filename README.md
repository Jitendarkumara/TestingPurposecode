    private void dataGridView1_CellContentClick(object sender, DataGridViewCellEventArgs e)
    {

        // Check if "Save" button is clicked
        //if (dataGridView1.Columns[e.ColumnIndex] is DataGridViewButtonColumn && e.RowIndex >= 0)
        //{
        //    string id =  dataGridView1.Rows[e.RowIndex].Cells["Coil_ID"].Value.ToString();
        //    string name = dataGridView1.Rows[e.RowIndex].Cells["Tag"].Value.ToString();


        //    // Call Update method
        //    UpdateData(id, name);
        //}


        if (e.RowIndex >= 0 && dataGridView1.Columns[e.ColumnIndex].Name == "EditSave")
        {
            DataGridViewRow row = dataGridView1.Rows[e.RowIndex];
            DataGridViewCell btnCell = row.Cells["EditSave"];

            if (btnCell.Value == null || btnCell.Value.ToString() == "Edit")
            {
                // Enable editing
                row.Cells["Coil_ID"].ReadOnly = false;
                btnCell.Value = "Save"; // Change button text
                dataGridView1.Refresh(); // Refresh UI
            }
            else if (btnCell.Value.ToString() == "Save")
            {
                try
                {
                    // Get updated values
                    string id = row.Cells["Tag"].Value.ToString();
                    string coilId = row.Cells["Coil_ID"].Value.ToString();

                    // Update query
                    string updateQuery = "UPDATE T_Event_Tracking SET Coil_id = :CoilID WHERE Id_app_tag_Event = :Id";

                    Db.DatabaseConnect();
                    using (OracleCommand cmd = new OracleCommand(updateQuery, Db.Con))
                    {
                        cmd.Parameters.Add(new OracleParameter(":CoilID", coilId));
                        cmd.Parameters.Add(new OracleParameter(":Id", id));

                        //int rowsAffected = cmd.ExecuteNonQuery();
                        //if (rowsAffected > 0)
                        //{
                        //    MessageBox.Show("Record updated successfully.");
                        //}
                        //else
                        //{
                        //    MessageBox.Show("No record updated.");
                        //}
                    }
                    Db.ConClose();

                    // Disable editing and change button text back to Edit
                    row.Cells["Coil_ID"].ReadOnly = true;
                    btnCell.Value = "Edit"; // Change button text back
                    dataGridView1.Refresh(); // Refresh UI
                }
                catch (Exception ex)
                {
                    MessageBox.Show("Error updating record: " + ex.Message);
                }
            }
        }
    
}
