public int CheckTimeDuration(DateTime StartTime,DateTime EndTime)
{

    Db.DatabaseConnect();
    string s = " select TCIP_PRODUCT_COIL, TCIP_CIL_START_TIME ,TCIP_CIL_END_TIME from T_COL_COIL_INFO_PDO where TCIP_CIL_START_TIME between :StartTime " +
        "and :EndTime or TCIP_CIL_END_TIME between :StartTime and :EndTime ";

    // Create the OracleCommand
    DataTable dt = new DataTable();
    string coilId, Start_Time, End_Time;
  

    using (OracleCommand cmd = new OracleCommand(s, Db.Con))
    {
        // Create and bind the parameter
        cmd.Parameters.Add(new OracleParameter("StartTime", StartTime));
        cmd.Parameters.Add(new OracleParameter("EndTime", EndTime));
        // Execute the command
        OracleDataAdapter da = new OracleDataAdapter();
       
        da.Fill(dt);
         coilId = dt.Rows[0][0].ToString();
      Start_Time = dt.Rows[0][1].ToString();
   End_Time = dt.Rows[0][2].ToString();
        
            

        
    }
    if(dt.Rows.Count > 0)
    {
        MessageBox.Show(coilId + "Aready exist between" + Start_Time + "And" + End_Time);
        return 1;
    }
    else
    {
        return 0;
    }
  
}
K541541212	10-JUN-25 11.04.11.000000000 AM	10-JUN-25 11.11.07.000000000 AM
