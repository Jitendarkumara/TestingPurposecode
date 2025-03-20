
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
