 public void LoadEventTrackGrid()
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
         btnEditSave.Name = "EditSave";
         btnEditSave.Text = "Edit"; // Initial button text
         btnEditSave.UseColumnTextForButtonValue = false; // Allow dynamic text change
         btnEditSave.DefaultCellStyle.Alignment = DataGridViewContentAlignment.MiddleCenter;
         btnEditSave.FlatStyle = FlatStyle.Popup;
         dataGridView1.Columns.Add(btnEditSave);

         // Initialize button text in each row
         foreach (DataGridViewRow row in dataGridView1.Rows)
         {
             row.Cells["EditSave"].Value = "Edit";
         }

         // Attach event handler
         dataGridView1.CellContentClick += dataGridView1_CellContentClick;

         Db.ConClose();
     }
     catch (Exception ex)
     {
         MessageBox.Show("Error loading data: " + ex.Message);
     }
 }
