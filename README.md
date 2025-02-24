 public DataTable getChartdata()
 {
     string mill = "Mill1";
    
     SqlCommand cmd = new SqlCommand("Proc_GetMonthlyEms_Model_data", cn);
     cmd.CommandType = CommandType.StoredProcedure;
     cmd.Parameters.Add(@mill,mill)
     SqlDataAdapter da = new SqlDataAdapter(cmd);
     DataTable dt = new DataTable();
     da.Fill(dt);

     return dt;
 }
