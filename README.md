   private void dataGridView2_CellContentClick(object sender, DataGridViewCellEventArgs e)
   {
       if (txtmodified.Text != "" && txtRemark.Text != "")
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
                           //cmd.Parameters.Add(new OracleParameter("coilId", coilId));
                           // cmd.ExecuteNonQuery();
                           string remark = txtRemark.Text;
                           string InsertQuery = "INSERT INTO  t_ccl_update_status(COIL_ID,UPDATED_BY,DATE_TIME,TABLE_MODIFIED,REMARK) VALUES(:COIL_ID,:Updated_by,:TIME,:Table,:Remark`)";

                           OracleCommand cmdInsert = new OracleCommand(InsertQuery, Db.Con);
                           cmdInsert.CommandType = CommandType.Text;


                           string updatedby = txtmodified.Text;
                           cmdInsert.Parameters.Add(":COIL_ID", OracleDbType.Varchar2).Value = Convert.ToString(coilId);
                           cmdInsert.Parameters.Add(":Updated_by", OracleDbType.Varchar2).Value = updatedby;
                           cmdInsert.Parameters.Add(":TIME", OracleDbType.TimeStamp).Value = DateTime.Now;
                           cmdInsert.Parameters.Add(":Table", OracleDbType.Varchar2).Value = "T_COIL_LOC_Temp";
                           cmdInsert.Parameters.Add(":Remark", OracleDbType.Varchar2).Value = remark;
                           int rowinserted = cmd.ExecuteNonQuery();

                           if (rowinserted > 0)
                           {

                               MessageBox.Show("Record updated successfully.");
                           }

                       }
                       Db.ConClose();

                       // Remove row from DataGridView
                       dataGridView2.Rows.RemoveAt(e.RowIndex);

                       MessageBox.Show("Record deleted successfully!", "Danger", MessageBoxButtons.OK, MessageBoxIcon.Stop);
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
