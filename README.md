 public void FetchAllTextValues()
 {
     try
     {
         Db.DatabaseConnect();
         //string s = "SELECT CHEM_DRYER_TEMP_ACT, FIN_OVN_TEMP_Z1_ACT, FIN_OVN_TEMP_Z2_ACT, FIN_OVN_TEMP_Z3_ACT, FIN_OVN_TEMP_Z4_ACT," +
         //           " PRIME_OVN_TEMP_Z1_ACT, PRIME_OVN_TEMP_Z2_ACT, PRIME_OVN_TEMP_Z3_ACT, TLL_TENSION_ACT, BRUSH_TANK_TEMP," +
         //           " BRUSH_TANK_CONDUCTIVITY, ALKALI_TANK_WATER_LVL, HOT_AIR_DRY_1_TEMP_SCL, HOT_AIR_DRY_2_TEMP_SCL, HOT_AIR_DRY_3_TEMP_SCL " +
         //           " FROM t_periodic_value_log WHERE DATE_TIME = (SELECT MAX(DATE_TIME) FROM T_PERIODIC_VALUE_LOG)";
         string s = "SELECT ENTRY_ACCUMULATOR_POS_PERCENT,PROCESS_LINE_SPEED_ACT, CHEM_DRYER_TEMP_ACT, FIN_OVN_TEMP_Z1_ACT, FIN_OVN_TEMP_Z2_ACT, FIN_OVN_TEMP_Z3_ACT, FIN_OVN_TEMP_Z4_ACT," +
                    " PRIME_OVN_TEMP_Z1_ACT, PRIME_OVN_TEMP_Z2_ACT, PRIME_OVN_TEMP_Z3_ACT, TLL_TENSION_ACT, BRUSH_TANK_TEMP," +
                    " BRUSH_TANK_CONDUCTIVITY, ALKALI_TANK_WATER_LVL, HOT_AIR_DRY_1_TEMP_SCL, HOT_AIR_DRY_2_TEMP_SCL, HOT_AIR_DRY_3_TEMP_SCL " +
                    " FROM  V_LATEST_PERIODIC_VALUE";
         OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
         DataTable dtTrack = new DataTable();
         da.Fill(dtTrack);
         Db.ConClose();

         if (dtTrack.Rows.Count > 0)
         {
             // Assign values to textboxes
             textBoxChemDryTemp.Text = dtTrack.Rows[0]["CHEM_DRYER_TEMP_ACT"].ToString();
             textBoxFOZ1.Text = dtTrack.Rows[0]["FIN_OVN_TEMP_Z1_ACT"].ToString();
             textBoxFOZ2.Text = dtTrack.Rows[0]["FIN_OVN_TEMP_Z2_ACT"].ToString();
             textBoxFOZ3.Text = dtTrack.Rows[0]["FIN_OVN_TEMP_Z3_ACT"].ToString();
             textBoxFOZ4.Text = dtTrack.Rows[0]["FIN_OVN_TEMP_Z4_ACT"].ToString();
             textBoxPOZ1.Text = dtTrack.Rows[0]["PRIME_OVN_TEMP_Z1_ACT"].ToString();
             textBoxPOZ2.Text = dtTrack.Rows[0]["PRIME_OVN_TEMP_Z2_ACT"].ToString();
             textBoxPOZ3.Text = dtTrack.Rows[0]["PRIME_OVN_TEMP_Z3_ACT"].ToString();
             textBoxTLLTEN.Text = dtTrack.Rows[0]["TLL_TENSION_ACT"].ToString();
             textBoxBrushTankTemp.Text = dtTrack.Rows[0]["BRUSH_TANK_TEMP"].ToString();
             textBoxBrushTankCond.Text = dtTrack.Rows[0]["BRUSH_TANK_CONDUCTIVITY"].ToString();
             textBoxAlkaliLevel.Text = dtTrack.Rows[0]["ALKALI_TANK_WATER_LVL"].ToString();
             textBoxblower1.Text = dtTrack.Rows[0]["HOT_AIR_DRY_1_TEMP_SCL"].ToString();
             textBoxblower2.Text = dtTrack.Rows[0]["HOT_AIR_DRY_2_TEMP_SCL"].ToString();
             textBoxblower3.Text = dtTrack.Rows[0]["HOT_AIR_DRY_3_TEMP_SCL"].ToString();
         }
         else
         {
             MessageBox.Show("No data found!");
         }

     }
     catch (Exception ex) { }
    

 }

 public void EtryExitAccumulator()
{
    try
    {
        Db.DatabaseConnect();
        string query = "select  ENTRY_ACCUMULATOR_POS_PERCENT FROM V_LATEST_PERIODIC_VALUE";
        OracleDataAdapter oda = new OracleDataAdapter(query, Db.Con);
        DataTable dtActual = new DataTable();
        oda.Fill(dtActual);
        if (dtActual.Rows.Count > 0)
        {
            if (Convert.ToInt32(dtActual.Rows[0].ItemArray[0]) >= 100)
            {
                // txtActual.Text = dtActual.Rows[0].ItemArray[0].ToString();
                progressBarEntry.Value = 100;
            }
            else {
                // txtActual.Text = dtActual.Rows[0].ItemArray[0].ToString();
                progressBarEntry.Value = Convert.ToInt32(dtActual.Rows[0].ItemArray[0]);
            }
           
        }


        ////progressBarEntry.Increment(7);
        //Random rnd = new Random();

        //for (int i = 0; i < 5; i++)
        //{
        //    progressBarEntry.Value = rnd.Next(1, 99);

        //}



        // lblEntryAccu.Text = progressBarEntry.Value.ToString() + "%";
        label72.Text = progressBarEntry.Value.ToString() + "%";
        // pictEntAccu.Height = 200 - progressBarEntry.Value * 2;
        pictureBox45.Height = 200 - progressBarEntry.Value * 2;
        
        //txtSet.Text = progressBarEntry.Value.ToString();

        //progressBar Exit;

        string query1 = "select  EXIT_ACCUMULATOR_POS_PERCENT FROM T_PERIODIC_VALUE_LOG  ORDER BY DATE_TIME DESC  fetch first 1 row only";
        OracleDataAdapter oda1 = new OracleDataAdapter(query1, Db.Con);
        DataTable dtActual1 = new DataTable();
        oda1.Fill(dtActual1);
        if (dtActual1.Rows.Count > 0)
        {
            if (Convert.ToInt32(dtActual1.Rows[0].ItemArray[0]) >= 100)
            {
                // txtActual.Text = dtActual.Rows[0].ItemArray[0].ToString();
                progressBarExit.Value = 100;
            }
            else
            {
                // txtActual.Text = dtActual.Rows[0].ItemArray[0].ToString();
                progressBarExit.Value = Convert.ToInt32(dtActual1.Rows[0].ItemArray[0]);

            }
            
        }




        //Random rnd1 = new Random();

        //for (int i = 0; i < 8; i++)
        //{
        //    progressBarExit.Value = rnd1.Next(1, 99);
        //}
        // lblExitAccu.Text = progressBarExit.Value.ToString() + "%";
        label73.Text = progressBarExit.Value.ToString() + "%";
        
        //txtActual.Text = (progressBarExit.Value + 1).ToString();
        pictureBox47.Height = 200 - progressBarExit.Value * 2;
    }
    catch (Exception ex)
    {
        MessageBox.Show(ex.Message);
    }
}
  public void MillActualValue()
  {
      Db.DatabaseConnect();
      string query = "select  PROCESS_LINE_SPEED_ACT FROM V_LATEST_PERIODIC_VALUE";
      OracleDataAdapter oda = new OracleDataAdapter(query, Db.Con);
      DataTable dtActual = new DataTable();
      oda.Fill(dtActual);
      if (dtActual.Rows.Count > 0)
      {
          txtSpeed.Text = dtActual.Rows[0].ItemArray[0].ToString();

          if(txtSpeed.Text=="0")
          {
              Por1timer.Stop();
              Por2timer.Stop();
              Recoiler1_Timer.Stop();
              Recoiler2_Timer.Stop();
          }
          else
          {

              Db.DatabaseConnect();
              string queryT_UNCOILER_SEL = "select * from T_UNCOILER_SEL FETCH order by WRITE_TIMESTAMP DESC fetch first 1 row only";
              OracleDataAdapter odaT_UNCOILER_SEL = new OracleDataAdapter(queryT_UNCOILER_SEL, Db.Con);
              DataTable dtT_UNCOILER_SEL = new DataTable();
              odaT_UNCOILER_SEL.Fill(dtT_UNCOILER_SEL);
              if(dtT_UNCOILER_SEL.Rows.Count > 0)
              {
                  if (dtT_UNCOILER_SEL.Rows[0][0].ToString()== "UNC_1_SELECTED")
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

              //string queryT_RECOILER_SEL = "select * from T_RECOILER_SEL FETCH order by WRITE_TIMESTAMP DESC fetch first 1 row only";
              //OracleDataAdapter odaT_RECOILER_SEL = new OracleDataAdapter(queryT_RECOILER_SEL, Db.Con);
              //DataTable dtT_RECOILER_SEL = new DataTable();
              //odaT_RECOILER_SEL.Fill(dtT_RECOILER_SEL);
              //if (dtT_RECOILER_SEL.Rows.Count > 0)
              //{
              //    if (dtT_RECOILER_SEL.Rows[0][0].ToString() == "RNC_1_SELECTED")
              //    {

              //        Recoiler1_Timer.Start();
              //        Recoiler2_Timer.Stop();

              //    }
              //    else
              //    {
              //        Recoiler2_Timer.Start();
              //        Recoiler1_Timer.Stop();

              //    }
              //}
              Db.ConClose();




          }
       ;
      }
  }
