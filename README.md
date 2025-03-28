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

        // Disable editing and change button text back to "Edit"
        row.Cells["Coil_ID"].ReadOnly = true;
        btnCell.Value = "Edit"; // Explicitly reset the button text
        dataGridView1.RefreshEdit(); // Refresh the specific cell
        dataGridView1.Refresh(); // Refresh UI

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