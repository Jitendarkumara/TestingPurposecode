public void LoadEventTrackGrid()
{
    try
    {
        Db.DatabaseConnect();

        string query = "SELECT Id_app_tag_Event, Coil_id FROM T_Event_Tracking";
        OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
        DataTable DtPDI = new DataTable();
        da.Fill(DtPDI);

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
            Text = "Edit",
            FlatStyle = FlatStyle.Popup
        };
        dataGridView1.Columns.Add(btnEditSave);

        dataGridView1.CellContentClick -= dataGridView1_CellContentClick;
        dataGridView1.CellContentClick += dataGridView1_CellContentClick;

        Db.ConClose();
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error loading data: " + ex.Message);
    }
}