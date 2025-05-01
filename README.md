 private void btnGo_Click(object sender, EventArgs e)
 {
     try
     {
         if (string.IsNullOrEmpty(txtSearchCoilNumb.Text))
         {
             MessageBox.Show("Please enter the coil number.");
             return; // Exit if no coil number is provided.
         }

         string tempCoilNum = txtSearchCoilNumb.Text.Trim();
         dgvPDO.SelectionMode = DataGridViewSelectionMode.FullRowSelect;
         BsPdo = new BindingSource();
         BsPdo.DataSource = dtPdo;
         BsPdo.Filter = string.Format("CGD_ID_COIL = '{0}'", tempCoilNum); // Ensure the filter is correctly formatted.

         // Use parameterized SQL query to prevent SQL injection
         string query = "SELECT CGD_ID_COIL FROM T_PDO_INFO WHERE CGD_ID_COIL = :coilNum";

         using (OracleCommand cmd = new OracleCommand(query, Db.Con))
         {
             cmd.Parameters.Add(new OracleParameter(":coilNum", tempCoilNum));

             OracleDataAdapter da = new OracleDataAdapter(cmd);
             DataTable dt = new DataTable();
             da.Fill(dt);

             dgvPDO.DataSource = dt;
             dgvPDO.AutoGenerateColumns = false;
             dgvPDO.RowsDefaultCellStyle.BackColor = Color.WhiteSmoke;
             dgvPDO.AlternatingRowsDefaultCellStyle.BackColor = Color.LightGray;
         }
         PDO.General GP1 = (PDO.General)Application.OpenForms["General"];
         GP1.LoadGenInfoData();

         Db.ConClose(); // Close the connection after the operation
     }
     catch (Exception ex)
     {
         // Handle exceptions properly
         MessageBox.Show($"An error occurred: {ex.Message}", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
     }
 }
