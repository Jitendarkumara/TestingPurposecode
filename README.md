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
        dataGridView1.Columns.Add(col1);

        DataGridViewTextBoxColumn col2 = new DataGridViewTextBoxColumn();
        col2.DataPropertyName = "Coil_id"; // Must match database column name
        col2.HeaderText = "Coil ID";
        col2.Name = "Coil_ID";
        dataGridView1.Columns.Add(col2);

        // Add the Edit Button Column
        DataGridViewButtonColumn btnEdit = new DataGridViewButtonColumn();
        btnEdit.HeaderText = "Action";
        btnEdit.Text = "Update";
        btnEdit.Name = "Edit";
        btnEdit.UseColumnTextForButtonValue = true;
        btnEdit.DefaultCellStyle.BackColor = Color.FromArgb(192, 0, 0);
        btnEdit.DefaultCellStyle.Alignment = DataGridViewContentAlignment.MiddleCenter;
        btnEdit.FlatStyle = FlatStyle.Popup;
        dataGridView1.Columns.Add(btnEdit);

        Db.ConClose();
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error loading data: " + ex.Message);
    }
}