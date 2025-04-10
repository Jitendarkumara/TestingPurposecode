 private void LoadPdiRptGridSearch(DateTime selectedDate, DateTime TosearchDate, string shift)
 {
     try
     {
         DateTime startDate = selectedDate.Date;                   // Start time: 10 Sep 2024 12:00 AM
         DateTime endDate = TosearchDate.AddDays(1).AddSeconds(-1); // End time: 10 Sep 2024 11:59:59 PM

         // Format the dates as strings in 'YYYY-MM-DD HH24:MI:SS' format
         string formattedStartDate = startDate.ToString("yyyy-MM-dd HH:mm:ss");
         string formattedEndDate = endDate.ToString("yyyy-MM-dd HH:mm:ss");

         Db.DatabaseConnect();
     //   string connectionString = "your_connection_string"; // Replace with your database connection string
     string query = @"SELECT t_col_cot_pdi_l3.tc_coil_number, t_col_cot_pdi_l3.TC_WEIGHT,TC_ID_MESSAGE
              FROM t_col_cot_pdi_l3
              WHERE REGEXP_LIKE(t_col_cot_pdi_l3.tc_id_message, '^\d{4}-\d{2}-\d{2}-\d{2}\.\d{2}\.\d{2}\.\d{6}$')
              AND TO_TIMESTAMP(t_col_cot_pdi_l3.tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') BETWEEN
              TO_TIMESTAMP(:startDate, 'YYYY-MM-DD HH24:MI:SS')
              AND TO_TIMESTAMP(:endDate, 'YYYY-MM-DD HH24:MI:SS') and substr(t_col_cot_pdi_l3.tc_id_message, 12,8) BETWEEN
             :StartShift And :EndShift
              AND t_col_cot_pdi_l3.tc_coil_number NOT IN ('D1000', 'D2000') order by TO_TIMESTAMP(t_col_cot_pdi_l3.tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') desc";
             //AND 
             //    TO_TIMESTAMP(t_col_cot_pdi_l3.tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') BETWEEN :StartShift AND :EndShift 
            
             //;";

     // Dynamically compute the time ranges based on the selected shift and date
     string startShift, endShift;
         string strDate = selectedDate.Date.ToString("yyyy-MM-dd HH:mm:ss");                   // Start time: 10 Sep 2024 12:00 AM
         string enDate = TosearchDate.AddDays(1).AddSeconds(-1).ToString("yyyy-MM-dd HH:mm:ss");
         switch (shift)
     {
         case "A":
                 // shift1 = (selectedDate.AddHours(6)).ToString("HH:mm:ss");
                 //    startShift = (selectedDate.AddHours(6)).ToString("yyyy-MM-dd HH:mm:ss");  // 06:00:00
                 //endShift = (selectedDate.AddHours(14)).ToString("yyyy-MM-dd HH:mm:ss");  // 14:00:00
                 startShift = (selectedDate.AddHours(6)).ToString("HH.mm.ss");  // 06:00:00
                 endShift = (selectedDate.AddHours(14)).ToString("HH.mm.ss");

                 break;
         case "B":
             startShift = (selectedDate.AddHours(14)).ToString("HH.mm.ss"); // 14:00:00
             endShift = (selectedDate.AddHours(22)).ToString("HH.mm.ss");  // 22:00:00
             break;
         case "C":
             startShift = (selectedDate.AddHours(22)).ToString("HH.mm.ss");        // 22:00:00
             endShift = (selectedDate.AddDays(1).AddHours(6)).ToString("HH.mm.ss"); // Next day 06:00:00
             break;
         case "ALL":
                 // End time: 10 Sep 2024 11:59:59 PM
                 startShift = startDate.ToString("HH:mm:ss");
                 endShift = endDate.ToString("HH:mm:ss");
                 // Format the dates as strings in 'YYYY-MM-DD HH24:MI:SS' format
                 //   startShift = strDate.ToString("HH.mm.ss");
                 // endShift = enDate.ToString("HH.mm.ss");
                 break;
                 default:
                 throw new Exception("Envalide Shift");
             //return;
     }

    
         // using (SqlConnection connection = new SqlConnection(Db.Con))
         OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);

         // Pass the formatted dates as strings to the query
         da.SelectCommand.Parameters.Add(new OracleParameter(":startDate", strDate));
         da.SelectCommand.Parameters.Add(new OracleParameter(":endDate", enDate));
         da.SelectCommand.Parameters.Add(new OracleParameter(":StartShift", startShift));
         da.SelectCommand.Parameters.Add(new OracleParameter(":EndShift", endShift));
         // da.SelectCommand.Parameters.Add(new OracleParameter(":SelectedShift", shift));

         string PDIquery = @"SELECT t_col_cot_pdi_l3.tc_coil_number, t_col_cot_pdi_l3.TC_WEIGHT,TC_ID_MESSAGE FROM t_col_cot_pdi_l3  
              WHERE REGEXP_LIKE(t_col_cot_pdi_l3.tc_id_message, '^\d{4}-\d{2}-\d{2}-\d{2}\.\d{2}\.\d{2}\.\d{6}$') AND" +
             " TO_TIMESTAMP(t_col_cot_pdi_l3.tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') BETWEEN TO_TIMESTAMP('"+ strDate + "', 'YYYY-MM-DD HH24:MI:SS') AND " +
             "TO_TIMESTAMP('"+endDate+"', 'YYYY-MM-DD HH24:MI:SS') and substr(t_col_cot_pdi_l3.tc_id_message, 12,8) BETWEEN" +
             "'"+startShift+"' and '"+endShift+"' AND t_col_cot_pdi_l3.tc_coil_number NOT IN ('D1000', 'D2000') order by TO_TIMESTAMP(t_col_cot_pdi_l3.tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') desc";

         DtPDI = new DataTable();
         da.Fill(DtPDI);
         dataGridViewRptPdi.DataSource = DtPDI;
         dataGridViewRptPdi.AutoGenerateColumns = true;

        
         
     }
     catch (Exception ex)
     {
         MessageBox.Show("Error: " + ex.Message);
     }
 }
