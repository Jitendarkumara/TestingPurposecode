private void dataGridView1_CellContentClick(object sender, DataGridViewCellEventArgs e)
{
    if (txtbox_modifiedBy.Text != "" && txtbox_Remark.Text != "")
    {
        if (e.RowIndex >= 0 && dataGridView1.Columns[e.ColumnIndex].Name == "EditSave")
        {
            DataGridViewRow row = dataGridView1.Rows[e.RowIndex];
            DataGridViewCell btnCell = row.Cells["EditSave"];

            if (btnCell.Value == null || btnCell.Value.ToString() == "Edit")
            {
                // Enable editing
                row.Cells["Coil_ID"].ReadOnly = false;
                dataGridView1.CurrentCell = row.Cells["Coil_ID"];
                dataGridView1.BeginEdit(true);
                btnCell.Value = "Save";
                btnCell.Style.BackColor = Color.Red;
                btnCell.Style.ForeColor = Color.Blue;
            }
            else if (btnCell.Value.ToString() == "Save")
            {
                try
                {
                    string id = row.Cells["Tag"].Value?.ToString();
                    string coilId = row.Cells["Coil_ID"].Value?.ToString();

                    if (string.IsNullOrEmpty(id) || string.IsNullOrEmpty(coilId))
                    {
                        MessageBox.Show("Tag or Coil_ID cannot be empty.");
                        return;
                    }

                    string updateQuery = "UPDATE T_Event_Tracking SET Coil_id = :CoilID WHERE Id_app_tag_Event = :Id";

                    Db.DatabaseConnect();
                    using (OracleCommand cmd = new OracleCommand(updateQuery, Db.Con))
                    {
                        cmd.Parameters.Add(new OracleParameter("CoilID", coilId));
                        cmd.Parameters.Add(new OracleParameter("Id", id));

                        int rowsAffected = cmd.ExecuteNonQuery();

                        if (rowsAffected > 0)
                        {
                            string remark = txtbox_Remark.Text;
                            string updatedBy = txtbox_modifiedBy.Text;

                            string insertQuery = "INSERT INTO t_ccl_update_status(COIL_ID, UPDATED_BY, DATE_TIME, TABLE_MODIFIED, REMARK) " +
                                                 "VALUES(:COIL_ID, :Updated_by, :TIME, :Table, :Remark)";

                            using (OracleCommand cmdInsert = new OracleCommand(insertQuery, Db.Con))
                            {
                                cmdInsert.CommandType = CommandType.Text;
                                cmdInsert.Parameters.Add(":COIL_ID", OracleDbType.Varchar2).Value = coilId;
                                cmdInsert.Parameters.Add(":Updated_by", OracleDbType.Varchar2).Value = updatedBy;
                                cmdInsert.Parameters.Add(":TIME", OracleDbType.TimeStamp).Value = DateTime.Now;
                                cmdInsert.Parameters.Add(":Table", OracleDbType.Varchar2).Value = "T_COIL_TRACKING";
                                cmdInsert.Parameters.Add(":Remark", OracleDbType.Varchar2).Value = remark;

                                int rowInserted = cmdInsert.ExecuteNonQuery();
                                if (rowInserted > 0)
                                {
                                    MessageBox.Show("Record updated successfully.");
                                }
                            }
                        }
                        else
                        {
                            MessageBox.Show("No record updated. Check if the ID exists.");
                        }
                    }
                    Db.ConClose();
                    LoadEventTrackGrid();
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
        MessageBox.Show("Kindly enter 'Modified By' and 'Remark'.");
    }
}