  private void Support_And_Maintainence_Load(object sender, EventArgs e)
  {
      LoadEventTrackGrid();
  }
  public void LoadEventTrackGrid()
  {
      try
      {
          Db.DatabaseConnect();

          string query = "SELECT Id_app_tag_Event, Coil_id FROM T_Event_Tracking";
          OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
          DataTable DtPDI = new DataTable();
          da.Fill(DtPDI);

          // Ensure columns are cleared before setting DataSource
          dataGridView1.Columns.Clear();
          dataGridView1.AutoGenerateColumns = false;
          dataGridView1.DataSource = DtPDI;

          // Add columns dynamically
          DataGridViewTextBoxColumn col1 = new DataGridViewTextBoxColumn
          {
              DataPropertyName = "Id_app_tag_Event",
              HeaderText = "Tag",
              Name = "Tag",
              ReadOnly = true
          };
          dataGridView1.Columns.Add(col1);

          DataGridViewTextBoxColumn col2 = new DataGridViewTextBoxColumn
          {
              DataPropertyName = "Coil_id",
              HeaderText = "Coil ID",
              Name = "Coil_ID",
              ReadOnly = true // Initially read-only
          };
          dataGridView1.Columns.Add(col2);

          // Add Edit/Save Button Column
          DataGridViewButtonColumn btnEditSave = new DataGridViewButtonColumn
          {
              HeaderText = "Action",
              Name = "EditSave",
              Text = "Edit", // Initial button text
              UseColumnTextForButtonValue = true, // Button will always show this text
              FlatStyle = FlatStyle.Popup
          };
          dataGridView1.Columns.Add(btnEditSave);

          // Remove duplicate event handler binding
          dataGridView1.CellContentClick -= dataGridView1_CellContentClick;
          dataGridView1.CellContentClick += dataGridView1_CellContentClick;

          Db.ConClose();
      }
      catch (Exception ex)
      {
          MessageBox.Show("Error loading data: " + ex.Message);
      }
  }


  private void dataGridView1_CellContentClick(object sender, DataGridViewCellEventArgs e)
  {
      // Ensure the clicked cell is a button and a valid row index
      if (e.RowIndex >= 0 && dataGridView1.Columns[e.ColumnIndex].Name == "EditSave")
      {
          DataGridViewRow row = dataGridView1.Rows[e.RowIndex];
          DataGridViewCell btnCell = row.Cells["EditSave"];

          if (btnCell.Value == null || btnCell.Value.ToString() == "Edit")
          {
              // Enable editing for relevant cells
              row.Cells["Coil_ID"].ReadOnly = false;
              row.Cells["Tag"].ReadOnly = false; // Enable this if needed
              btnCell.Value = "Save"; // Change button text
              dataGridView1.Refresh(); // Refresh UI
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
                  row.Cells["Tag"].ReadOnly = true; // If you enabled editing for Tag
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
