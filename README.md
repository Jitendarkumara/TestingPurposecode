  exec Proc_GetMonthlyEms_Model_data 'Mill1', 'API1.Panelno_5_ENTRY1234_SIDE_TRIMMER_CHOPPER_PINCH','2025-03-18 22:00:00','2025-03-19 22:00:00','All'

  18-03-2025 06:00:00}

  public DataTable getChartdata(string Mill, string Feeder, DateTime selectedDate, string status, string Shift)
{
    DateTime fromDate, toDate;

    switch (Shift.ToUpper())
    {
        case "A": // Shift A: 6 AM - 2 PM
            fromDate = selectedDate.Date.AddHours(6);
            toDate = selectedDate.Date.AddHours(14);
            break;

        case "B": // Shift B: 2 PM - 10 PM
            fromDate = selectedDate.Date.AddHours(14);
            toDate = selectedDate.Date.AddHours(22);
            break;

        case "C": // Shift C: 10 PM (previous day) - 6 AM (current day)
            fromDate = selectedDate.Date.AddDays(-1).AddHours(22);
            toDate = selectedDate.Date.AddHours(6);
            break;

        case "ALL": // Full day data: 12 AM - 11:59 PM
        default:
            fromDate = selectedDate.Date;          // 12:00 AM
            toDate = selectedDate.Date.AddDays(1); // 12:00 AM (next day)
            break;
    }

    SqlCommand cmd = new SqlCommand("Proc_GetMonthlyEms_Model_data", Pimscn);
    cmd.CommandType = CommandType.StoredProcedure;

    // Define parameters (No need to send Shift)
    cmd.Parameters.Add("@mill", SqlDbType.VarChar).Value = Mill;
    cmd.Parameters.Add("@feeder", SqlDbType.VarChar).Value = Feeder;
    cmd.Parameters.Add("@selectedDate", SqlDbType.DateTime).Value = fromDate;
    cmd.Parameters.Add("@ToDate", SqlDbType.DateTime).Value = toDate;
    cmd.Parameters.Add("@status", SqlDbType.VarChar).Value = status;

    SqlDataAdapter da = new SqlDataAdapter(cmd);
    DataTable dt = new DataTable();
    da.Fill(dt);

    return dt;
