 public IActionResult GetKWHData(string MillName, string feederName)
 {
  

         MachineSectionDal M = new MachineSectionDal();

         List<Chartdata> Data = new List<Chartdata>();
         DataTable dt = new DataTable();
         dt = M.getChartdata(MillName, feederName);

         for (int i = 0; i < dt.Rows.Count; i++)
         {
             Chartdata chartdata = new Chartdata();
             chartdata.TimeStamp = Convert.ToDateTime(dt.Rows[i]["TimeStamp"].ToString());

             chartdata.Millname = (dt.Rows[i]["Mill_Id"].ToString());

             chartdata.KWH = (Convert.ToDouble(dt.Rows[i]["TotalActvalue"]));
             Data.Add(chartdata);
         }
         return Json(Data);
     
    
 }
