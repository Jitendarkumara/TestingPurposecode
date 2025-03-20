
            try
            {
                Db.DatabaseConnect();
                string s = @"select Id_app_tag_Event,Coil_id from T_Event_Tracking";

                OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
            DataTable  DtPDI = new DataTable();
                da.Fill(DtPDI);
                dataGridView1.AutoGenerateColumns = false;
                dataGridView1.Columns.Add("Id_app_tag_Event", "Tag");
                dataGridView1.Columns.Add("Coil_id", "Coil_id");
                dataGridView1.DataSource = DtPDI;
                dataGridView1.Columns["Coil_ID"].HeaderText = "Coil_id"; 
                dataGridView1.Columns["Tag"].HeaderText = "Id_app_tag_Event";

              
                Db.ConClose();
}
   dataGridViewCellStyle3.Alignment = System.Windows.Forms.DataGridViewContentAlignment.MiddleCenter;
   dataGridViewCellStyle3.BackColor = System.Drawing.Color.FromArgb(((int)(((byte)(192)))), ((int)(((byte)(0)))), ((int)(((byte)(0)))));
   this.Edit.DefaultCellStyle = dataGridViewCellStyle3;
   this.Edit.FlatStyle = System.Windows.Forms.FlatStyle.Popup;
   this.Edit.HeaderText = "Action";
   this.Edit.Name = "Edit";
   this.Edit.Text = "Update";
   this.Edit.UseColumnTextForButtonValue = true;
   // 
   // Tag
   // 
   this.Tag.HeaderText = "Id_Tag";
   this.Tag.Name = "Tag";
   // 
   // Coil_ID
   // 
   this.Coil_ID.HeaderText = "Coil_ID";
   this.Coil_ID.Name = "Coil_ID";
   // 
