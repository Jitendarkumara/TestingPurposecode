 private async void timerText_Tick(object sender, EventArgs e)
 {
     if (Db.DatabaseConnectCheck())
     {
         await Task.Run(() =>
         {
            // AutoProcessingPor();
            // RecoilCoilId();
            // AutoPor1ColorProcessig();
            //AutoPor2ColorProcessig();
            //Here I am adding New method insted of above 4 method 
             ProcessEventTracking(); // Added by jitu 

           //  MillActualValue();
           //  EtryExitAccumulator();
          //   FetchAllTextValues();
             //Here I am adding New method insted of above 3 method 
             FetchAndUpdateUI(); // Added by jitu 
         });

         // UI-related work must be run on UI thread
         LoadTextBoxData();
     }
 }
 public void FetchAndUpdateUI()
 {
     try
     {
         Db.DatabaseConnect();

         if (Db.Con == null || Db.Con.State != ConnectionState.Open)
             throw new Exception("Database connection is not available.");

         string query = @"SELECT ENTRY_ACCUMULATOR_POS_PERCENT,
                         EXIT_ACCUMULATOR_POS_PERCENT,
                         PROCESS_LINE_SPEED_ACT,
                         CHEM_DRYER_TEMP_ACT,
                         FIN_OVN_TEMP_Z1_ACT, FIN_OVN_TEMP_Z2_ACT, FIN_OVN_TEMP_Z3_ACT, FIN_OVN_TEMP_Z4_ACT,
                         PRIME_OVN_TEMP_Z1_ACT, PRIME_OVN_TEMP_Z2_ACT, PRIME_OVN_TEMP_Z3_ACT,
                         TLL_TENSION_ACT, BRUSH_TANK_TEMP, BRUSH_TANK_CONDUCTIVITY,
                         ALKALI_TANK_WATER_LVL,
                         HOT_AIR_DRY_1_TEMP_SCL, HOT_AIR_DRY_2_TEMP_SCL, HOT_AIR_DRY_3_TEMP_SCL
                  FROM V_LATEST_PERIODIC_VALUE";

         DataTable dt = new DataTable();
         using (OracleDataAdapter da = new OracleDataAdapter(query, Db.Con))
         {
             da.Fill(dt);
         }

         if (dt.Rows.Count == 0)
         {
            // MessageBox.Show("⚠️ No data found in V_LATEST_PERIODIC_VALUE.");
             return;
         }

         DataRow row = dt.Rows[0];

         // --- Update TextBoxes (Temperature, Tension, etc.)
         textBoxChemDryTemp.Text = row["CHEM_DRYER_TEMP_ACT"].ToString();
         textBoxFOZ1.Text = row["FIN_OVN_TEMP_Z1_ACT"].ToString();
         textBoxFOZ2.Text = row["FIN_OVN_TEMP_Z2_ACT"].ToString();
         textBoxFOZ3.Text = row["FIN_OVN_TEMP_Z3_ACT"].ToString();
         textBoxFOZ4.Text = row["FIN_OVN_TEMP_Z4_ACT"].ToString();
         textBoxPOZ1.Text = row["PRIME_OVN_TEMP_Z1_ACT"].ToString();
         textBoxPOZ2.Text = row["PRIME_OVN_TEMP_Z2_ACT"].ToString();
         textBoxPOZ3.Text = row["PRIME_OVN_TEMP_Z3_ACT"].ToString();
         textBoxTLLTEN.Text = row["TLL_TENSION_ACT"].ToString();
         textBoxBrushTankTemp.Text = row["BRUSH_TANK_TEMP"].ToString();
         textBoxBrushTankCond.Text = row["BRUSH_TANK_CONDUCTIVITY"].ToString();
         textBoxAlkaliLevel.Text = row["ALKALI_TANK_WATER_LVL"].ToString();
         textBoxblower1.Text = row["HOT_AIR_DRY_1_TEMP_SCL"].ToString();
         textBoxblower2.Text = row["HOT_AIR_DRY_2_TEMP_SCL"].ToString();
         textBoxblower3.Text = row["HOT_AIR_DRY_3_TEMP_SCL"].ToString();

         // --- Update Entry Accumulator ProgressBar
         int entryPercent = Convert.ToInt32(row["ENTRY_ACCUMULATOR_POS_PERCENT"]);
         progressBarEntry.Value = Math.Min(entryPercent, 100);
         label72.Text = progressBarEntry.Value + "%";
         pictureBox45.Height = 200 - progressBarEntry.Value * 2;

         // --- Update Exit Accumulator ProgressBar
         int exitPercent = Convert.ToInt32(row["EXIT_ACCUMULATOR_POS_PERCENT"]);
         progressBarExit.Value = Math.Min(exitPercent, 100);
         label73.Text = progressBarExit.Value + "%";
         pictureBox47.Height = 200 - progressBarExit.Value * 2;

         // --- Update Mill Speed and Timers
         string speedStr = row["PROCESS_LINE_SPEED_ACT"].ToString();
         txtSpeed.Text = speedStr;

         if (speedStr == "0")
         {
             Por1timer.Stop();
             Por2timer.Stop();
             Recoiler1_Timer.Stop();
             Recoiler2_Timer.Stop();
         }
         else
         {
             // fetch uncoupler selector (only when running)
             string uncoilerQuery = "SELECT * FROM T_UNCOILER_SEL ORDER BY WRITE_TIMESTAMP DESC FETCH FIRST 1 ROW ONLY";
             DataTable dtUncoiler = new DataTable();
             using (OracleDataAdapter daUnc = new OracleDataAdapter(uncoilerQuery, Db.Con))
             {
                 daUnc.Fill(dtUncoiler);
             }

             if (dtUncoiler.Rows.Count > 0)
             {
                 if (dtUncoiler.Rows[0][0].ToString() == "UNC_1_SELECTED")
                 {
                     Por1timer.Start();
                     Por2timer.Stop();
                 }
                 else
                 {
                     Por2timer.Start();
                     Por1timer.Stop();
                 }
             }
         }
     }
     catch (Exception ex)
     {
         MessageBox.Show("Error in FetchAndUpdateUI: " + ex.Message);
     }
     finally
     {
         Db.ConClose();
     }
 }
 DataTable dtTrack = new DataTable();
 public void ProcessEventTracking()
 {
     try
     {
         Db.DatabaseConnect();
       
         string query = "SELECT ID_APP_TAG_EVENT, COIL_ID FROM T_EVENT_TRACKING";
         using (OracleDataAdapter da = new OracleDataAdapter(query, Db.Con))
         {
            
             da.Fill(dtTrack);

             Db.ConClose();
         }
         if (dtTrack.Rows.Count == 0) return;

         // 🔹 AUTO PROCESSING POR
         var uncRow = dtTrack.AsEnumerable()
             .FirstOrDefault(r => r["ID_APP_TAG_EVENT"].ToString() == "UNC_SELECTED");

         if (uncRow != null)
         {
             string coilId = uncRow["COIL_ID"].ToString();

             if (!string.IsNullOrEmpty(textBox17.Text))
             {
                 if (coilId == textBox17.Text)
                 {
                     btnPor1.BackColor = Color.Red;
                     btnPor1.Text = "Processing";
                     btnPor1End.Enabled = false;

                     btnPor2.BackColor = Color.FromArgb(59, 89, 152);
                     btnPor2.Enabled = false;
                     btnEnd.Enabled = false;

                     timerEntry.Start();
                     dat = DateTime.Now;
                 }
                 else
                 {
                     btnPor1.BackColor = Color.FromArgb(59, 89, 152);
                     btnPor1.Text = "START";
                 }
             }
             else if (!string.IsNullOrEmpty(textBox16.Text))
             {
                 if (coilId == textBox16.Text)
                 {
                     btnPor2.BackColor = Color.Red;
                     btnPor2.Text = "Processing";
                     btnEnd.Enabled = false;

                     btnPor1.BackColor = Color.FromArgb(59, 89, 152);
                     btnPor1.Enabled = false;
                     btnPor1End.Enabled = false;

                     Por2timer.Start();
                     timerEntry.Start();
                     dat = DateTime.Now;
                 }
                 else
                 {
                     Por2timer.Stop();
                     btnPor2.BackColor = Color.FromArgb(59, 89, 152);
                     btnPor2.Text = "START";
                 }
             }
         }

         // 🔹 RECOILER COIL
         var recoilRow = dtTrack.AsEnumerable()
             .FirstOrDefault(r => r["ID_APP_TAG_EVENT"].ToString() == "RECOILER");

         if (recoilRow != null && recoilRow["COIL_ID"] != DBNull.Value)
         {
             RecoilerSaddleFill();
         }
         else
         {
             txtRecoiller.Text = "";
         }

         // 🔹 AUTO POR1 COLOR PROCESSING
         if (btnPor1.Text == "Processing")
         {
             string por1Coil = txtPor1.Text;

             if (dtTrack.Rows.Count > 1 && dtTrack.Rows[1][1].ToString() == por1Coil)
             {
                 diagonal1.BackLineColor = Color.Red;
                 pict1.BackColor = Color.Red;
                 diagonalSep1.backLineColorSep = Color.Red;
                 pict3.BackColor = Color.Red;
                 diagonal3.BackLineColor = Color.Red;
                 pict5.BackColor = Color.Red;
                 pict6.BackColor = Color.Red;
                 diagonal4.BackLineColor = Color.Red;
                 pict7.BackColor = Color.Red;
                 diagonalSep2.backLineColorSep = Color.Red;
                 pict8.BackColor = Color.Red;
                 pict9.BackColor = Color.Red;
                 pict10.BackColor = Color.Red;
                 pict11.BackColor = Color.Red;
             }
             if (dtTrack.Rows.Count > 2 && dtTrack.Rows[2][1].ToString() == por1Coil)
             {
                 ColorTllT0BR6();
             }
             if (dtTrack.Rows.Count > 3 && dtTrack.Rows[3][1].ToString() == por1Coil)
             {
                 ColorBr6ToStr6();
             }
             if (dtTrack.Rows.Count > 4 && dtTrack.Rows[4][1].ToString() == por1Coil)
             {
                 ColorStr6ToBr9();
                 btnPor1End.Enabled = true;
             }
             if (dtTrack.Rows.Count > 5 && dtTrack.Rows[5][1].ToString() == por1Coil)
             {
                 ColorBr9ToRecoil();
             }
             if (btnPor1.Text == "START")
             {
                 PorColorBlack();
             }
         }

         // 🔹 AUTO POR2 COLOR PROCESSING
         if (btnPor2.Text == "Processing")
         {
             string por2Coil = txtPor2.Text;

             if (dtTrack.Rows.Count > 1 && dtTrack.Rows[1][1].ToString() == por2Coil)
             {
                 pict3.BackColor = Color.Red;
                 diagonal3.BackLineColor = Color.Red;
                 pict5.BackColor = Color.Red;
                 pict6.BackColor = Color.Red;
                 diagonal4.BackLineColor = Color.Red;
                 pict7.BackColor = Color.Red;
                 diagonalSep2.backLineColorSep = Color.Red;
                 pict8.BackColor = Color.Red;
                 pict9.BackColor = Color.Red;
                 pict10.BackColor = Color.Red;
                 pict11.BackColor = Color.Red;
             }
             if (dtTrack.Rows.Count > 2 && dtTrack.Rows[2][1].ToString() == por2Coil)
             {
                 ColorTllT0BR6();
             }
             if (dtTrack.Rows.Count > 3 && dtTrack.Rows[3][1].ToString() == por2Coil)
             {
                 ColorBr6ToStr6();
             }
             if (dtTrack.Rows.Count > 4 && dtTrack.Rows[4][1].ToString() == por2Coil)
             {
                 ColorStr6ToBr9();
                 btnEnd.Enabled = true;
             }
             if (dtTrack.Rows.Count > 5 && dtTrack.Rows[5][1].ToString() == por2Coil)
             {
                 ColorBr9ToRecoil();
             }
             if (btnPor2.Text == "START")
             {
                 PorColorBlack();
             }
         }
     }
     catch (Exception ex)
     {
         MessageBox.Show(ex.Message);
     }
 }
  public class DBhelper
 {
     public OracleConnection Con;
     public string ConnectioString = System.Configuration.ConfigurationManager.ConnectionStrings["ServiceURL"].ToString();

     // Define an event to notify when an error occurs
     public event Action<string> OnDatabaseError;

   

     public void DatabaseConnect()
     {
         try
         {
             if (Con == null)
             {
                 Con = new OracleConnection(ConnectioString);
             }

             if (Con.State != ConnectionState.Open)
             {
                 Con.Open();
                 Console.WriteLine("Database connection established successfully.");
             }
             else
             {
                 Console.WriteLine("Connection is already open.");
             }
         }
         catch (InvalidOperationException ex)
         {
             Console.WriteLine("Invalid operation: " + ex.Message);
             LogErrorToFile(ex.ToString(), "DatabaseConnect");
         }
         catch (OracleException ex)
         {
             Console.WriteLine("Oracle database error: " + ex.Message);
             LogErrorToFile(ex.ToString(), "DatabaseConnect");
             ConClose(); // Ensure proper cleanup
         }
         catch (Exception ex)
         {
             Console.WriteLine("General error: " + ex.Message);
             LogErrorToFile(ex.ToString(), "DatabaseConnect");
             ConClose();
         }
     }
     public void LogErrorToFile(string errorMessage, string Method)
     {
         try
         {
             string desktopPath = Environment.GetFolderPath(Environment.SpecialFolder.Desktop);
             string logFilePath = Path.Combine(desktopPath, "DatabaseErrorLog.txt");

             if (!File.Exists(logFilePath))
             {
                 string header = $"Database Error Log\nCreated on: {DateTime.Now}\n\n";
                 File.WriteAllText(logFilePath, header);
             }

             // Append the error message and stack trace
             File.AppendAllText(logFilePath, $"Error: {errorMessage}\nMethod: {Method}\nTime: {DateTime.Now}\n\n");
         }
         catch (Exception ex)
         {
             Console.WriteLine("Error writing to log file: " + ex.Message);
         }
     }
     public bool DatabaseConnectCheck()
     {
         using (OracleConnection con = new OracleConnection(ConnectioString))
         {
             try
             {
                 con.Open();
                 return true; // Connection successful
             }
             catch
             {
                 if (con != null)
                 {
                     if (con.State == ConnectionState.Open)
                     {
                         con.Close();
                         Console.WriteLine("Database connection closed.");
                     }
                     con.Dispose();
                 }
                 return false; // Connection failed
             }

         }
     }

     public void ConClose()
     {
         if (Con != null && Con.State == ConnectionState.Open)
         {
             Con.Close();
             Console.WriteLine("Database connection closed.");
         }

         if (Con != null)
         {
             Con.Dispose();
             Con = null; // Prevent using disposed object
         }
     }
 }
