  public DataTable getChartdata(string Mill, string Feeder,DateOnly selectedDate)
  {
      //string mill = "Mill1";
      DateOnly Todate= selectedDate-1;
      SqlCommand cmd = new SqlCommand("Proc_GetMonthlyEms_Model_data", Pimscn);
      cmd.CommandType = CommandType.StoredProcedure;
      cmd.Parameters.Add("@mill", SqlDbType.VarChar).Value = Mill;
      cmd.Parameters.Add("@feeder", SqlDbType.VarChar).Value = Feeder;
      cmd.Parameters.Add("@selectedDate", SqlDbType.VarChar).Value = selectedDate;
      cmd.Parameters.Add("@ToDate", SqlDbType.VarChar).Value = Todate;
      SqlDataAdapter da = new SqlDataAdapter(cmd);
      DataTable dt = new DataTable();
      da.Fill(dt);

      return dt;
  }
