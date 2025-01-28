       private void LoadPdiRptGridSearch(DateTime selectedDate, string shift)
       {
           try
           {
               Db.DatabaseConnect();
           //   string connectionString = "your_connection_string"; // Replace with your database connection string
           string query = @"SELECT t_col_cot_pdi_l3.tc_coil_number, t_col_cot_pdi_l3.TC_WEIGHT,TC_ID_MESSAGE
                    FROM t_col_cot_pdi_l3
                    WHERE REGEXP_LIKE(t_col_cot_pdi_l3.tc_id_message, '^\d{4}-\d{2}-\d{2}-\d{2}\.\d{2}\.\d{2}\.\d{6}$')
                    AND TO_TIMESTAMP(t_col_cot_pdi_l3.tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') BETWEEN
                    TO_TIMESTAMP(:StartShift, 'YYYY-MM-DD HH24:MI:SS')
                    AND TO_TIMESTAMP(:EndShift, 'YYYY-MM-DD HH24:MI:SS')
                    AND t_col_cot_pdi_l3.tc_coil_number NOT IN ('D1000', 'D2000')";
                   //AND 
                   //    TO_TIMESTAMP(t_col_cot_pdi_l3.tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') BETWEEN :StartShift AND :EndShift 
                  
                   //;";

           // Dynamically compute the time ranges based on the selected shift and date
           string startShift, endShift;
           switch (shift)
           {
               case "A":
                   startShift = (selectedDate.AddHours(6)).ToString("yyyy-MM-dd HH:mm:ss");  // 06:00:00
                   endShift = (selectedDate.AddHours(14)).ToString("yyyy-MM-dd HH:mm:ss");  // 14:00:00
                    
                   break;
               case "B":
                   startShift = (selectedDate.AddHours(14)).ToString("yyyy-MM-dd HH:mm:ss"); // 14:00:00
                   endShift = (selectedDate.AddHours(22)).ToString("yyyy-MM-dd HH:mm:ss");  // 22:00:00
                   break;
               case "C":
                   startShift = (selectedDate.AddHours(22)).ToString("yyyy-MM-dd HH:mm:ss");        // 22:00:00
                   endShift = (selectedDate.AddDays(1).AddHours(6)).ToString("yyyy-MM-dd HH:mm:ss"); // Next day 06:00:00
                   break;
               case "ALL":
                       DateTime startDate = selectedDate.Date;                   // Start time: 10 Sep 2024 12:00 AM
                   DateTime endDate = startDate.AddDays(1).AddSeconds(-1); // End time: 10 Sep 2024 11:59:59 PM

                   // Format the dates as strings in 'YYYY-MM-DD HH24:MI:SS' format
                   startShift = startDate.ToString("yyyy-MM-dd HH:mm:ss");
                   endShift = endDate.ToString("yyyy-MM-dd HH:mm:ss");
                    break;
                   //return;
           }

          
               // using (SqlConnection connection = new SqlConnection(Db.Con))
               OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);

               // Pass the formatted dates as strings to the query
               da.SelectCommand.Parameters.Add(new OracleParameter(":StartShift", startShift));
               da.SelectCommand.Parameters.Add(new OracleParameter(":EndShift", endShift));
              // da.SelectCommand.Parameters.Add(new OracleParameter(":SelectedShift", shift));

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

