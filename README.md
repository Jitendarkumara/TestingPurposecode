  public void AutoProcessingPor()
  {
      try
      {
          Db.DatabaseConnect();
          string query = "select COIL_ID FROM T_EVENT_TRACKING WHERE ID_APP_TAG_EVENT='UNC_SELECTED'";
          OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
          DataTable dt = new DataTable();
          da.Fill(dt);
          Db.ConClose();
          if (dt.Rows.Count > 0)
          {
              if (textBox17.Text != String.Empty)
              {
                  if (dt.Rows[0].ItemArray[0].ToString() == textBox17.Text)
                  {

                      btnPor1.BackColor = Color.Red;
                      btnPor1.Text = "Processing";
                      btnPor1End.Enabled = false;

                      btnPor2.BackColor = Color.FromArgb(59, 89, 152);
                      btnPor2.Enabled = false;
                      btnEnd.Enabled = false;
                      //tRACKING EVENT
                      //UpdTrackEventPOR1();

                     // Por1timer.Start();

                      timerEntry.Start();
                      //timerColor.Start();
                      dat = DateTime.Now;
                  }
                  else if(dt.Rows[0].ItemArray[0].ToString() != textBox17.Text)
                  {
                     // Por1timer.Stop();   // commented by jitu on 05-FEB-2025
                      btnPor1.BackColor = Color.FromArgb(59, 89, 152);
                      btnPor1.Text = "START";
                  }
              }
             else if (textBox16.Text != String.Empty)
              {
                  if (dt.Rows[0].ItemArray[0].ToString() == textBox16.Text)
                  {

                      btnPor2.BackColor = Color.Red;
                      btnPor2.Text = "Processing";
                      btnEnd.Enabled = false;

                      btnPor1.BackColor = Color.FromArgb(59, 89, 152);
                      btnPor1.Enabled = false;
                      btnPor1End.Enabled = false;
                      Por2timer.Start();
                      //Tracking Event
                      // UpdTrackEventPOR2();

                      timerEntry.Start();
                      
                      dat = DateTime.Now;
                  }
                  else if (dt.Rows[0].ItemArray[0].ToString() != textBox16.Text)
                  {
                      Por2timer.Stop();
                      btnPor2.BackColor = Color.FromArgb(59, 89, 152);
                      btnPor2.Text = "START";
                  }
              }
          }
          Db.ConClose();
      }
      catch (Exception ex)
      {
          MessageBox.Show(ex.Message);
      }
  }

        public void RecoilCoilId()
  {
      try
      {
          Db.DatabaseConnect();
          // string s = "select COIL_ID from T_EVENT_TRACKING Where ID_APP_TAG_EVENT='L1_COIL_EXT' ";
          string s = "select COIL_ID from T_EVENT_TRACKING Where ID_APP_TAG_EVENT='RECOILER' ";
          OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
          DataTable dtRecoile = new DataTable();
          da.Fill(dtRecoile);
          Db.ConClose();
          if (dtRecoile.Rows.Count > 0)
          {
             if (dtRecoile.Rows[0][0] != DBNull.Value)
              {
                 // textBoxRecoiler1.Text = dtRecoile.Rows[0][0].ToString();
                  RecoilerSaddleFill();
              }
              else
              {
                  txtRecoiller.Text = "";
              }
          }
         
      }
      catch (Exception ex)
      {
          MessageBox.Show(ex.Message);
      }
  }
 public void AutoPor1ColorProcessig()
 {
     try
     {
         Db.DatabaseConnect();
         string s = "select ID_APP_TAG_EVENT,COIL_ID from T_EVENT_TRACKING  ";
         OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
         DataTable dtTrack = new DataTable();
         da.Fill(dtTrack);
         Db.ConClose();
         if (dtTrack.Rows.Count > 0)
         {
             if (btnPor1.Text == "Processing")
             {
                 //UPTO TLL
                 if (dtTrack.Rows[1][1].ToString() == txtPor1.Text)
                 {
                    
                         diagonal1.Invalidate();
                         diagonal1.BackLineColor = Color.Red;
                    
                         pict1.BackColor = Color.Red;
                    
                         diagonalSep1.Invalidate();
                         diagonalSep1.backLineColorSep = Color.Red;
                    
                         pict3.BackColor = Color.Red;                          
                         //pict4.BackColor = Color.Red;
                    
                         diagonal3.Invalidate();
                         diagonal3.BackLineColor = Color.Red;
                   
                         pict5.BackColor = Color.Red;                            
                         pict6.BackColor = Color.Red;
                     
                         diagonal4.Invalidate();
                         diagonal4.BackLineColor = Color.Red;
                    
                         pict7.BackColor = Color.Red;
                    
                         diagonalSep2.Invalidate();
                         diagonalSep2.backLineColorSep = Color.Red;
                    
                         pict8.BackColor = Color.Red;                           
                         pict9.BackColor = Color.Red;                           
                         pict10.BackColor = Color.Red;                          
                         pict11.BackColor = Color.Red;
                     
                 }
                 //UPTO TLL TO BR6
                 if (dtTrack.Rows[2][1].ToString() == txtPor1.Text)
                 {
                     ColorTllT0BR6();
                 }
                 //UPTO BR6 TO  ST6
                 if (dtTrack.Rows[3][1].ToString() == txtPor1.Text)
                 {
                     ColorBr6ToStr6();
                 }
                 //UPTO ST6 TO BR9
                 if (dtTrack.Rows[4][1].ToString() == txtPor1.Text)
                 { 
                     ColorStr6ToBr9();
                     btnPor1End.Enabled = true;
                 }
                 //UPTO Br9 RECOILE
                 if (dtTrack.Rows[5][1].ToString() == txtPor1.Text)
                 {
                     ColorBr9ToRecoil();
                    
                 }
                 if (btnPor1.Text == "START")
                 {
                     PorColorBlack();
                 }
                 else
                 {
                     
                 }
                 
             }
         }
     }
     catch (Exception ex)
     {
         MessageBox.Show(ex.Message);
     }
 }
 public void AutoPor2ColorProcessig()
 {
     try
     {
         Db.DatabaseConnect();
         string s = "select ID_APP_TAG_EVENT,COIL_ID from T_EVENT_TRACKING  ";
         OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
         DataTable dtTrack = new DataTable();
         da.Fill(dtTrack);
         Db.ConClose();
         if (dtTrack.Rows.Count > 0)
         {
             if (btnPor2.Text == "Processing")
             {
                 //UPTO POR2 TO TLL
                 if (dtTrack.Rows[1][1].ToString() == txtPor2.Text)
                 {

                   //  diagonal2.Invalidate();
                   //  diagonal2.BackLineColor = Color.Red;
                   //  pict2.BackColor = Color.Red;                            
                     pict3.BackColor = Color.Red;
                  //   pict4.BackColor = Color.Red;
                     diagonal3.Invalidate();
                     diagonal3.BackLineColor = Color.Red;
                     pict5.BackColor = Color.Red;
                     pict6.BackColor = Color.Red;
                     diagonal4.Invalidate();
                     diagonal4.BackLineColor = Color.Red;
                     pict7.BackColor = Color.Red;
                     diagonalSep2.Invalidate();
                     diagonalSep2.backLineColorSep = Color.Red;
                     pict8.BackColor = Color.Red;
                     pict9.BackColor = Color.Red;
                     pict10.BackColor = Color.Red;
                     pict11.BackColor = Color.Red;

                 }
                 //UPTO TLL TO BR6
                 if (dtTrack.Rows[2][1].ToString() == txtPor2.Text)
                 {
                     ColorTllT0BR6();
                 }
                 //UPTO BR6 TO  ST6
                 if (dtTrack.Rows[3][1].ToString() == txtPor2.Text)
                 {
                     ColorBr6ToStr6();
                 }
                 //UPTO ST6 TO BR9
                 if (dtTrack.Rows[4][1].ToString() == txtPor2.Text)
                 {
                     ColorStr6ToBr9();
                     btnEnd.Enabled = true;
                 }
                 //UPTO Br9 RECOILE
                 if (dtTrack.Rows[5][1].ToString() == txtPor2.Text)
                 {
                     ColorBr9ToRecoil();
                    
                 }
                 if (btnPor2.Text == "START")
                 {
                     PorColorBlack();
                 }
                 else
                 {

                 }

             }
         }
     }
     catch (Exception ex)
     {
         MessageBox.Show(ex.Message);
     }
 }
