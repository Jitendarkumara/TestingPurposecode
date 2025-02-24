public IActionResult GetKWHData()
{
    MachineSectionDal M = new MachineSectionDal();
    List<Chartdata> Data = new List<Chartdata>();
    DataTable dt = M.getChartdata();

    HashSet<string> distinctMills = new HashSet<string>(); // Store unique mills

    for (int i = 0; i < dt.Rows.Count; i++)
    {
        Chartdata chartdata = new Chartdata
        {
            TimeStamp = Convert.ToDateTime(dt.Rows[i]["Timestamp"]).ToString("yyyy-MM-ddTHH:mm:ssZ"), // Ensure ISO format
            Millname = dt.Rows[i]["Mill_Id"].ToString(),
            KWH = Convert.ToDouble(dt.Rows[i]["TotalActvalue"])
        };

        Data.Add(chartdata);
        distinctMills.Add(chartdata.Millname); // Add unique mills
    }

    return Json(new { chartData = Data, mills = distinctMills.ToList() }); // Send chart data & unique mills
}