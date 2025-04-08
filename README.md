private void dataGridView2_CellContentClick(object sender, DataGridViewCellEventArgs e)
{
    if (txtmodified.Text != "" && txtRemark.Text != "")
    {
        if (e.ColumnIndex == dataGridView2.Columns["Delete"].Index && e.RowIndex >= 0)
        {
            string coilId = dataGridView2.Rows[e.RowIndex].Cells["Coil_ID"].Value.ToString();

            DialogResult result = MessageBox.Show($"Are you sure you want to delete Coil ID {coilId}?",
                "Confirm Delete", MessageBoxButtons.OKCancel, MessageBoxIcon.Warning);

            if (result == DialogResult.OK)
            {
                try
                {
                    Db.DatabaseConnect();

                    // Delete record
                    string deleteQuery = "DELETE FROM T_COIL_LOC_TEM WHERE coil_id = :coilId";
                    using (OracleCommand cmdDelete = new OracleCommand(deleteQuery, Db.Con))
                    {
                        cmdDelete.Parameters.Add(new OracleParameter("coilId", coilId));
                        cmdDelete.ExecuteNonQuery();
                    }

                    // Insert audit log
                    string insertQuery = "INSERT INTO t_ccl_update_status(COIL_ID, UPDATED_BY, DATE_TIME, TABLE_MODIFIED, REMARK) " +
                                         "VALUES(:COIL_ID, :Updated_by, :TIME, :Table, :Remark)";

                    using (OracleCommand cmdInsert = new OracleCommand(insertQuery, Db.Con))
                    {
                        cmdInsert.CommandType = CommandType.Text;
                        cmdInsert.Parameters.Add(":COIL_ID", OracleDbType.Varchar2).Value = coilId;
                        cmdInsert.Parameters.Add(":Updated_by", OracleDbType.Varchar2).Value = txtmodified.Text;
                        cmdInsert.Parameters.Add(":TIME", OracleDbType.TimeStamp).Value = DateTime.Now;
                        cmdInsert.Parameters.Add(":Table", OracleDbType.Varchar2).Value = "T_COIL_LOC_TEM";
                        cmdInsert.Parameters.Add(":Remark", OracleDbType.Varchar2).Value = txtRemark.Text;

                        int rowInserted = cmdInsert.ExecuteNonQuery();
                        if (rowInserted > 0)
                        {
                            MessageBox.Show("Record updated successfully.");
                        }
                    }

                    Db.ConClose();

                    // Remove row from UI
                    dataGridView2.Rows.RemoveAt(e.RowIndex);
                    MessageBox.Show("Record deleted successfully!", "Deleted", MessageBoxButtons.OK, MessageBoxIcon.Information);
                }
                catch (Exception ex)
                {
                    MessageBox.Show("Error deleting data: " + ex.Message, "Error", MessageBoxButtons.OK, MessageBoxIcon.Warning);
                }
            }
        }
    }
    else
    {
        MessageBox.Show("Kindly Enter Modified By and Remark");
    }
}