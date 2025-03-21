public DataTable getChartdata(string Mill, string Feeder, DateTime selectedDate,string status,string Shift)
{
 
        DateTime Todate = selectedDate.AddDays(+1); // Get the previous day

        SqlCommand cmd = new SqlCommand("Proc_GetMonthlyEms_Model_data", Pimscn);


        cmd.CommandType = CommandType.StoredProcedure;

        // Correctly define parameters
        cmd.Parameters.Add("@mill", SqlDbType.VarChar).Value = Mill;
        cmd.Parameters.Add("@feeder", SqlDbType.VarChar).Value = Feeder;
        cmd.Parameters.Add("@selectedDate", SqlDbType.Date).Value = selectedDate;
        cmd.Parameters.Add("@ToDate", SqlDbType.Date).Value = Todate;
    cmd.Parameters.Add("@status", SqlDbType.VarChar).Value = status;
   

        SqlDataAdapter da = new SqlDataAdapter(cmd);


            DataTable dt = new DataTable();
        da.Fill(dt);
        return dt;
      
    
}
