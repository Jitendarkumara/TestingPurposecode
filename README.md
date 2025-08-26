 private void timerText_Tick(object sender, EventArgs e)
 {
     if (Db.DatabaseConnectCheck())
     {
         LoadTextBoxData();
         //FOR Auto Process compare evnt tracking table for Backend
         AutoProcessingPor();
         //FOR Auto Process when recoil is complete
         RecoilCoilId();
         
         AutoPor1ColorProcessig();
         AutoPor2ColorProcessig();
         //For Mill Actual Speed
         MillActualValue();
         EtryExitAccumulator();
         FetchAllTextValues();
     }

 }
