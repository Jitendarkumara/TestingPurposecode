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

 ALTER PROCEDURE [dbo].[Proc_GetMonthlyEms_Model_data]
    @mill NVARCHAR(50),
    @feeder NVARCHAR(200)
AS
BEGIN
    SET NOCOUNT ON;

    IF (@feeder = 'All')
    BEGIN
     --   SELECT TOP (100) 
	    SELECT  
            [Timestamp], 
            Mill_Id, 
            SUM(Act_value) AS TotalActValue
        FROM [PIMS_KHOPOLI].[dbo].[Ems_Model_output]
        WHERE Mill_Id = @mill
        GROUP BY [Timestamp], Mill_Id
        ORDER BY [Timestamp] DESC;
    END
    ELSE
    BEGIN
        SELECT TOP (100) 
            [Timestamp], 
            Mill_Id, 
            [Parameter], 
            SUM(ISNULL(Act_value,0)) AS TotalActValue
        FROM [PIMS_KHOPOLI].[dbo].[Ems_Model_output]
        WHERE Mill_Id = @mill AND [Parameter] = @feeder
        GROUP BY [Timestamp], Mill_Id, [Parameter]
        ORDER BY [Timestamp] DESC;
    END
END;
