DateTime startDate = searchDate.Date;                   // Start time: 10 Sep 2024 12:00 AM
 DateTime endDate = TosearchDate.AddDays(1).AddSeconds(-1); // End time: 10 Sep 2024 11:59:59 PM

 // Format the dates as strings in 'YYYY-MM-DD HH24:MI:SS' format
 string formattedStartDate = startDate.ToString("yyyy-MM-dd HH:mm:ss");
 string formattedEndDate = endDate.ToString("yyyy-MM-dd HH:mm:ss");
// DateTime startDate = searchDate.Date;                   // Start time: 10 Sep 2024 12:00 AM
// DateTime endDate = startDate.AddDays(1).AddSeconds(-1);

 Db.DatabaseConnect();
 string s = @"SELECT pdo.tcip_input_coil, pdo.tcip_product_coil ,pdo.tcpi_actual_wt,TCIP_CIL_END_TIME,pdo.L3_FLAG
      FROM t_col_coil_info_pdo pdo
      WHERE pdo.tcip_input_coil IN (
          SELECT t_col_cot_pdi_l3.tc_coil_number
          FROM t_col_cot_pdi_l3
          WHERE REGEXP_LIKE(t_col_cot_pdi_l3.tc_id_message, '^\d{4}-\d{2}-\d{2}-\d{2}\.\d{2}\.\d{2}\.\d{6}$')
             AND TO_TIMESTAMP(t_col_cot_pdi_l3.tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') BETWEEN
      TO_TIMESTAMP(:startDate, 'YYYY-MM-DD HH24:MI:SS')
      AND TO_TIMESTAMP(:endDate, 'YYYY-MM-DD HH24:MI:SS') and substr(t_col_cot_pdi_l3.tc_id_message, 12,8) BETWEEN
     :StartShift And :EndShift
          AND t_col_cot_pdi_l3.tc_coil_number NOT IN ('D1000', 'D2000')) order by TCIP_CIL_END_TIME desc";
 string startShift, endShift;
 switch (shift)
 {
     case "A":
         startShift = (searchDate.AddHours(6)).ToString("HH:mm:ss");  // 06:00:00
         endShift = (searchDate.AddHours(14)).ToString("HH:mm:ss");  // 14:00:00

         break;
     case "B":
         startShift = (searchDate.AddHours(14)).ToString("HH:mm:ss"); // 14:00:00
         endShift = (searchDate.AddHours(22)).ToString("HH:mm:ss");  // 22:00:00
         break;
     case "C":
         startShift = (searchDate.AddHours(22)).ToString("HH:mm:ss");        // 22:00:00
         endShift = (searchDate.AddDays(1).AddHours(6)).ToString("HH:mm:ss"); // Next day 06:00:00
         break;
     case "ALL":
      // End time: 10 Sep 2024 11:59:59 PM

         // Format the dates as strings in 'YYYY-MM-DD HH24:MI:SS' format
         startShift = startDate.ToString("HH:mm:ss");
         endShift = endDate.ToString("HH:mm:ss");

         break;
     default:
         throw new Exception("Envalide shift");

 }

 OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);

 // Pass the formatted dates as strings to the query

 da.SelectCommand.Parameters.Add(new OracleParameter(":startDate", formattedStartDate));
 da.SelectCommand.Parameters.Add(new OracleParameter(":endDate", formattedEndDate));
 da.SelectCommand.Parameters.Add(new OracleParameter(":StartShift", startShift));
 da.SelectCommand.Parameters.Add(new OracleParameter(":EndShift", endShift));
