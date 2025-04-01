    public void LoadCoilTempGrid()
    {
        try
        {
            Db.DatabaseConnect();

            string query = "select seq_No,coil_id,TOC from T_COIL_LOC_TEM";
            OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
            DataTable DtPDI = new DataTable();
            da.Fill(DtPDI);

            dataGridView2.Columns.Clear();
            dataGridView2.AutoGenerateColumns = false;
            dataGridView2.DataSource = null;  // Clear existing binding first
            dataGridView2.AllowUserToAddRows = false; // Prevent extra empty row
            dataGridView2.DataSource = DtPDI;

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

            // Add Edit/Save Button Column
            DataGridViewButtonColumn btnDelete = new DataGridViewButtonColumn
            {
                HeaderText = "Action",
                Name = "Delete",
              

                FlatStyle = FlatStyle.Popup
            };
            dataGridView2.Columns.Add(btnDelete);

            // Ensure all rows have "Edit" as the initial button text
            foreach (DataGridViewRow row in dataGridView2.Rows)
            {
                if (!row.IsNewRow) // Prevent setting button text on the new row
                {
                    row.Cells["Delete"].Value = "Delete";
                    row.Cells["Delete"].Style.BackColor = Color.AliceBlue; //SystemColors.Window;
                    row.Cells["Delete"].Style.ForeColor = Color.Red;

                }
            }

            // Explicitly remove the empty row if it still exists
            if (dataGridView2.Rows.Count > 0 && dataGridView2.Rows[dataGridView2.Rows.Count - 1].IsNewRow)
            {
                dataGridView2.Rows.RemoveAt(dataGridView2.Rows.Count - 1);
            }

           // dataGridView2.CellContentClick -= dataGridView2_CellContentClick;
           // dataGridView2.CellContentClick += dataGridView2_CellContentClick;

            Db.ConClose();
        }
        catch (Exception ex)
        {
            MessageBox.Show("Error loading data: " + ex.Message);
        }
    }
