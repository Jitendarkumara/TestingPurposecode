private void LoadData()
{
    try
    {
        Db.DatabaseConnect();
        string query = "SELECT Id_app_tag_Event, Coil_id FROM T_Event_Tracking";

        OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
        DataTable DtPDI = new DataTable();
        da.Fill(DtPDI);

        dataGridView1.AutoGenerateColumns = false;
        dataGridView1.DataSource = DtPDI;

        // Clear existing columns to prevent duplication
        dataGridView1.Columns.Clear();

        // Add columns dynamically with correct mapping
        DataGridViewTextBoxColumn col1 = new DataGridViewTextBoxColumn();
        col1.DataPropertyName = "Id_app_tag_Event"; // Must match column name from database
        col1.HeaderText = "Tag";
        col1.Name = "Tag";
        col1.ReadOnly = true; // Prevent editing primary key
        dataGridView1.Columns.Add(col1);

        DataGridViewTextBoxColumn col2 = new DataGridViewTextBoxColumn();
        col2.DataPropertyName = "Coil_id"; // Must match database column name
        col2.HeaderText = "Coil ID";
        col2.Name = "Coil_ID";
        col2.ReadOnly = true; // Initially read-only
        dataGridView1.Columns.Add(col2);

        // Add Edit/Save Button Column
        DataGridViewButtonColumn btnEditSave = new DataGridViewButtonColumn();
        btnEditSave.HeaderText = "Action";
        btnEditSave.Text = "Edit";
        btnEditSave.Name = "EditSave";
        btnEditSave.UseColumnTextForButtonValue = true;
        btnEditSave.DefaultCellStyle.Alignment = DataGridViewContentAlignment.MiddleCenter;
        btnEditSave.FlatStyle = FlatStyle.Popup;
        dataGridView1.Columns.Add(btnEditSave);

        // Attach event handler for row updates
        dataGridView1.CellClick += dataGridView1_CellClick;

        Db.ConClose();
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error loading data: " + ex.Message);
    }
}

private void dataGridView1_CellClick(object sender, DataGridViewCellEventArgs e)
{
    if (e.RowIndex >= 0 && dataGridView1.Columns[e.ColumnIndex].Name == "EditSave")
    {
        DataGridViewRow row = dataGridView1.Rows[e.RowIndex];

        if (row.Cells["EditSave"].Value.ToString() == "Edit")
        {
            // Enable editing
            row.Cells["Coil_ID"].ReadOnly = false;
            row.Cells["EditSave"].Value = "Save"; // Change button text
        }
        else if (row.Cells["EditSave"].Value.ToString() == "Save")
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

                    int rowsAffected = cmd.ExecuteNonQuery();
                    if (rowsAffected > 0)
                    {
                        MessageBox.Show("Record updated successfully.");
                    }
                    else
                    {
                        MessageBox.Show("No record updated.");
                    }
                }
                Db.ConClose();

                // Disable editing and change button text back to Edit
                row.Cells["Coil_ID"].ReadOnly = true;
                row.Cells["EditSave"].Value = "Edit";
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error updating record: " + ex.Message);
            }
        }
    }
}