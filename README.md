public IActionResult GetKWHData(string MillName, string feederName)
{
    MachineSectionDal M = new MachineSectionDal();
    List<Chartdata> Data = new List<Chartdata>();
    DataTable dt = M.getChartdata(MillName, feederName);

    for (int i = 0; i < dt.Rows.Count; i++)
    {
        Chartdata chartdata = new Chartdata();

        // Handle NULL values safely
        chartdata.TimeStamp = dt.Rows[i]["TimeStamp"] != DBNull.Value 
            ? Convert.ToDateTime(dt.Rows[i]["TimeStamp"]) 
            : DateTime.MinValue;

        chartdata.Millname = dt.Rows[i]["Mill_Id"] != DBNull.Value 
            ? dt.Rows[i]["Mill_Id"].ToString() 
            : string.Empty;

        chartdata.KWH = dt.Rows[i]["TotalActValue"] != DBNull.Value 
            ? Convert.ToDouble(dt.Rows[i]["TotalActValue"]) 
            : 0.0; // Default value if NULL

        Data.Add(chartdata);
    }

    return Json(Data);
}