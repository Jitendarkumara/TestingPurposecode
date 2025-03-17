Timestamp	Mill_Id	Total_Act_Value	Running_Status	Alert	Thickness	Grade	CustName	Total_KWAH
2025-03-01 01:30:00.000	Ext-coating	81.4191558612511	1	1	0	NULL		0
2025-03-01 01:30:00.000	Finishing-1	46.3312245891429	1	0	0	NULL		0
2025-03-01 01:15:00.000	Finishing-2	8.68492327560671	1	1	0	NULL		0
2025-03-01 01:30:00.000	Mill1	37.3225406818092	0	1	0	NULL		0
2025-03-01 01:30:00.000	Mill2	17.2740543333348	0	1	0	NULL		0

WITH LatestTimestamp AS (
    SELECT 
        Mill_Id, 
        MAX(Timestamp) AS Latest_Timestamp
    FROM dbo.Ems_Model_output
    WHERE DATEPART(MINUTE, Timestamp) % 15 = 0
    GROUP BY Mill_Id
)
SELECT 
    e.Timestamp, 
    e.Mill_Id, 
    SUM(e.Act_value) AS Total_Act_Value, 
    MAX(e.Running_Status) AS Running_Status, 
    MAX(e.Alert) AS Alert, 
    MAX(e.Thickness) AS Thickness, 
    MAX(e.Grade) AS Grade, 
    MAX(e.CustName) AS CustName, 
    SUM(e.KWAH) AS Total_KWAH
FROM dbo.Ems_Model_output e
JOIN LatestTimestamp lt ON e.Mill_Id = lt.Mill_Id AND e.Timestamp = lt.Latest_Timestamp
GROUP BY e.Timestamp, e.Mill_Id
order by Mill_Id;

  MachineSectionDal M = new MachineSectionDal();
  DataTable dt = new DataTable();
  dt = M.getEmsMachineRunningData(); // Fetch data

  // Ensure DataTable has enough rows before accessing specific indexes
  if (dt.Rows.Count < 5)
  {
      return BadRequest("Insufficient data in DataTable.");
  }

  // Extracting machine running status values
  double mill1 = Convert.ToDouble(dt.Rows[4]["Running_Status"]),
         mill2 = Convert.ToDouble(dt.Rows[2]["Running_Status"]),
         finishing1 = Convert.ToDouble(dt.Rows[3]["Running_Status"]),
         finishing2 = Convert.ToDouble(dt.Rows[1]["Running_Status"]),
         extcoating = Convert.ToDouble(dt.Rows[0]["Running_Status"]);

  // Extracting alert values
  double sumMill1 = Convert.ToDouble(dt.Rows[4]["Alert"]),
         sumMill2 = Convert.ToDouble(dt.Rows[2]["Alert"]),
         sumFinishing1 = Convert.ToDouble(dt.Rows[3]["Alert"]),
         sumFinishing2 = Convert.ToDouble(dt.Rows[1]["Alert"]),
         sumExtcoating = Convert.ToDouble(dt.Rows[0]["Alert"]);
