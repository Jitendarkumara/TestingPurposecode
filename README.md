  public void LoadCoilTempGrid()
  {
      try
      {
          Db.DatabaseConnect();

          string query = "SELECT seq_No, coil_id, TOC FROM T_COIL_LOC_TEM";
          OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
          DataTable DtPDI = new DataTable();
          da.Fill(DtPDI);

          // Reset DataGridView before adding new data
          dataGridView2.Columns.Clear();
          dataGridView2.AutoGenerateColumns = false;
          dataGridView2.DataSource = null;
          dataGridView2.AllowUserToAddRows = false;
          dataGridView2.DataSource = DtPDI;

          // Set auto size properties for better UI
          dataGridView2.AutoSizeRowsMode = DataGridViewAutoSizeRowsMode.AllCells;
          dataGridView2.AutoSizeColumnsMode = DataGridViewAutoSizeColumnsMode.Fill;

          // Add columns dynamically
          DataGridViewTextBoxColumn col1 = new DataGridViewTextBoxColumn
          {
              DataPropertyName = "seq_No",
              HeaderText = "Sequence No.",
              Name = "Seq",
              ReadOnly = true
          };
          dataGridView2.Columns.Add(col1);

          DataGridViewTextBoxColumn col2 = new DataGridViewTextBoxColumn
          {
              DataPropertyName = "Coil_id",
              HeaderText = "Coil ID",
              Name = "Coil_ID",
              ReadOnly = true
          };
          dataGridView2.Columns.Add(col2);

          DataGridViewTextBoxColumn col3 = new DataGridViewTextBoxColumn
          {
              DataPropertyName = "TOC",
              HeaderText = "Date Time",
              Name = "dtTime",
              ReadOnly = true
          };
          dataGridView2.Columns.Add(col3);

          // Add Delete Button Column
          DataGridViewButtonColumn btnDelete = new DataGridViewButtonColumn
          {
              HeaderText = "DeleteAction",
              Name = "Delete",
              FlatStyle = FlatStyle.Popup,
              Text = "Delete",
              UseColumnTextForButtonValue = true // Ensures button text is displayed
          };
          dataGridView2.Columns.Add(btnDelete);
          // Add Edit/Save Button Column
          DataGridViewButtonColumn btnEditSave = new DataGridViewButtonColumn
          {
              HeaderText = "EditAction",
              Name = "EditSave",
              UseColumnTextForButtonValue = false, // Allow dynamic text

              FlatStyle = FlatStyle.Popup
          };
          dataGridView1.Columns.Add(btnEditSave);

          // Set button styles for each row
          foreach (DataGridViewRow row in dataGridView2.Rows)
          {
              if (!row.IsNewRow)
              {
                  row.Cells["Delete"].Style.BackColor = Color.AliceBlue;
                  row.Cells["Delete"].Style.ForeColor = Color.Red;
              }
          }

          // Ensure the last empty row is removed
          if (dataGridView2.Rows.Count > 0 && dataGridView2.Rows[dataGridView2.Rows.Count - 1].IsNewRow)
          {
              dataGridView2.Rows.RemoveAt(dataGridView2.Rows.Count - 1);
          }

          // Adjust DataGridView height dynamically
          AdjustGridHeight();

          Db.ConClose();
      }
      catch (Exception ex)
      {
          MessageBox.Show("Error loading data: " + ex.Message);
      }
  }

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
                      
                      string insertQuery = "INSERT INTO t_ccl_update_status (COIL_ID, UPDATED_BY, DATE_TIME, TABLE_MODIFIED, REMARK) " +
              "VALUES (:COIL_ID, :UPDATED_BY, :DATE_TIME, :TABLE_MODIFIED, :REMARK)";
                      

                          using (OracleCommand cmdInsert = new OracleCommand(insertQuery, Db.Con))
                      {
                          cmdInsert.CommandType = CommandType.Text;
                          cmdInsert.Parameters.Add(":COIL_ID", OracleDbType.Varchar2).Value = coilId;
                          cmdInsert.Parameters.Add(":UPDATED_BY", OracleDbType.Varchar2).Value = txtmodified.Text;
                          cmdInsert.Parameters.Add(":DATE_TIME", OracleDbType.TimeStamp).Value = DateTime.Now;
                          cmdInsert.Parameters.Add(":TABLE_MODIFIED", OracleDbType.Varchar2).Value = "T_COIL_LOC_TEM";
                          cmdInsert.Parameters.Add(":REMARK", OracleDbType.Varchar2).Value = txtRemark.Text;

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
