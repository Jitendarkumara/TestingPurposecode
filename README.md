MachineSectionDal M = new MachineSectionDal();
DataTable dt = M.getEmsMachineRunningData(); // Fetch data

// Check if DataTable has rows
if (dt.Rows.Count == 0)
{
    return BadRequest("No data available.");
}

// Create dictionaries to map Running_Status and Alert by Mill_Id
Dictionary<string, double> runningStatusMap = new Dictionary<string, double>();
Dictionary<string, double> alertMap = new Dictionary<string, double>();

foreach (DataRow row in dt.Rows)
{
    string millId = row["Mill_Id"].ToString();
    
    if (!string.IsNullOrEmpty(millId))
    {
        runningStatusMap[millId] = Convert.ToDouble(row["Running_Status"]);
        alertMap[millId] = Convert.ToDouble(row["Alert"]);
    }
}

// Assign values using safe dictionary lookups
double mill1 = runningStatusMap.ContainsKey("Mill1") ? runningStatusMap["Mill1"] : 0;
double mill2 = runningStatusMap.ContainsKey("Mill2") ? runningStatusMap["Mill2"] : 0;
double finishing1 = runningStatusMap.ContainsKey("Finishing-1") ? runningStatusMap["Finishing-1"] : 0;
double finishing2 = runningStatusMap.ContainsKey("Finishing-2") ? runningStatusMap["Finishing-2"] : 0;
double extcoating = runningStatusMap.ContainsKey("Ext-coating") ? runningStatusMap["Ext-coating"] : 0;

double sumMill1 = alertMap.ContainsKey("Mill1") ? alertMap["Mill1"] : 0;
double sumMill2 = alertMap.ContainsKey("Mill2") ? alertMap["Mill2"] : 0;
double sumFinishing1 = alertMap.ContainsKey("Finishing-1") ? alertMap["Finishing-1"] : 0;
double sumFinishing2 = alertMap.ContainsKey("Finishing-2") ? alertMap["Finishing-2"] : 0;
double sumExtcoating = alertMap.ContainsKey("Ext-coating") ? alertMap["Ext-coating"] : 0;