   private void dgvPDO_SelectionChanged(object sender, EventArgs e)
   {
       try
       {
           if (dgvPDO.SelectedRows.Count > 0)
           {
               PDOCoilNum = dgvPDO.SelectedRows[0].Cells[0].Value.ToString();
               //PDO.General Gp= new PDO.General();
               //PDO.Cleaning_Furnace Cl=new PDO.Cleaning_Furnace();
               //PDO.Galvanizing Gal = new PDO.Galvanizing();
               //PDO.Exit Ext=new PDO.Exit();
               if (btnGeneral.ForeColor == Color.Black)
               {
                   //Gp.TopLevel = false;
                   //Gp.AutoScroll = true;
                   //pnlPdo.Controls.Add(Gp);
                   //Gp.Show();

                   PDO.General GP1 = (PDO.General)Application.OpenForms["General"];
                   GP1.LoadGenInfoData();
               }
         }
       }




       }
