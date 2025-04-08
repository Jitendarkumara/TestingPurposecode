Oracle.ManagedDataAccess.Client.OracleException: 'ORA-01745: invalid host/bind variable name'

This exception was originally thrown at this call stack:
    [External Code]
    PDO.Support_And_Maintainence.dataGridView1_CellContentClick(object, System.Windows.Forms.DataGridViewCellEventArgs) in Support_And_Maintainence.cs

    private void dataGridView1_CellContentClick(object sender, DataGridViewCellEventArgs e)
{ if (txtbox_modifiedBy.Text != "" && txtbox_Remark.Text != "")
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
                dataGridView1.CurrentCell = row.Cells["Coil_ID"];
                dataGridView1.BeginEdit(true); // Start edit mode
                btnCell.Value = "Save"; // Change button text dynamically
                btnCell.Style.BackColor = Color.Red;
                btnCell.Style.ForeColor = Color.Blue;
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

                        //  int rowsAffected = cmd.ExecuteNonQuery();
                        int rowsAffected = 1;
                        if (rowsAffected > 0)
                        {
                            string remark = txtbox_Remark.Text;
                            string InsertQuery = "INSERT INTO  t_ccl_update_status(COIL_ID,UPDATED_BY,DATE_TIME,TABLE_MODIFIED,REMARK) VALUES(:COIL_ID,:Updated_by,:TIME,:Table,:Remark,)";

                            OracleCommand cmdInsert = new OracleCommand(InsertQuery, Db.Con);
                            cmdInsert.CommandType = CommandType.Text;


                            string updatedby = txtbox_modifiedBy.Text;
                            cmdInsert.Parameters.Add(":COIL_ID", OracleDbType.Varchar2).Value = Convert.ToString(coilId);
                            cmdInsert.Parameters.Add(":Updated_by", OracleDbType.Varchar2).Value = updatedby;
                            cmdInsert.Parameters.Add(":TIME", OracleDbType.TimeStamp).Value = DateTime.Now;
                            cmdInsert.Parameters.Add(":Table", OracleDbType.Varchar2).Value = "T_COIL_TRACKING";
                            cmdInsert.Parameters.Add(":Remark", OracleDbType.Varchar2).Value = remark;
                            int rowinserted= cmdInsert.ExecuteNonQuery();

                            if (rowinserted > 0)
                            {

                                MessageBox.Show("Record updated successfully.");
                            }
                           
                        }
                        else
                        {
                            MessageBox.Show("No record updated. Check if the ID exists.");
                        }
                    }
                    Db.ConClose();

                    // Disable editing and change button text back to Edit
                    // row.Cells["Coil_ID"].ReadOnly = true;
                    // btnCell.Value = "Edit"; // Change button text back
                    LoadEventTrackGrid();
                    // Accept changes to reflect updated data
                    //((DataTable)dataGridView1.DataSource).AcceptChanges();
                    // dataGridView1.Refresh(); // Refresh UI
                }
                catch (Exception ex)
                {
                    MessageBox.Show("Error updating record: " + ex.Message);
                }
            }
        }
    }
else
    {
        MessageBox.Show("Kindly Enter Modified By and Remark");
    }
}
