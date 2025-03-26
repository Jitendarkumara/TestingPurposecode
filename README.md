 public void LoadTextBoxData()
 {
     try
     {
         Db.DatabaseConnect();
         string s = "SELECT COIL_ID, LOC FROM T_COIL_LOC";
         OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
         DataTable dtText = new DataTable();
         da.Fill(dtText);
         Db.ConClose();

         // Clear text boxes initially
         textBox17.Text = "";
         textBox16.Text = "";

         // Loop through the rows and assign correct values based on LOC
         foreach (DataRow row in dtText.Rows)
         {
             if (row["LOC"].ToString() == "POR1")
             {
                 textBox17.Text = row["COIL_ID"].ToString();
             }
             else if (row["LOC"].ToString() == "POR2")
             {
                 textBox16.Text = row["COIL_ID"].ToString();
             }
         }
     }
     catch (Exception ex)
     {
        // MessageBox.Show("Error: " + ex.Message, "Database Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
     }
 }
