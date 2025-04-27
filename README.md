 public JsonResult Furnace_Delay()
        {
            string sql = string.Empty;
            DataTable dt = new DataTable();
            sql = string.Empty;
            sql = " Select Timestamp,SHIFT SHIFT,FURNACE,TYPE,Reason DELAY ,START_TIME ,END_TIME  From Demo.t_furnace_delays Where";
            //sql += "  TRUNC(Timestamp) = TO_DATE('" + Fdate + "', 'DD-MM-YYYY')";
            sql += "  TRUNC(Timestamp) ='26-APR-2025'";
            List<String> ListOfParam = new List<string>();
            ListOfParam.Add("SHIFT");
            ListOfParam.Add("FURNACE");
            ListOfParam.Add("TYPE");
            ListOfParam.Add("DELAY");
            ListOfParam.Add("START_TIME");
            ListOfParam.Add("END_TIME");
            String DelaysParams = JsonConvert.SerializeObject(ListOfParam, Formatting.None);
            dt = DAL.GetRecords(sql);
            sql = string.Empty;
            string DelayData = JsonConvert.SerializeObject(dt, Formatting.None);
            //return Json(new { DelaysParams, DelayData }, JsonRequestBehavior.AllowGet);
            return Json(new { data = DelaysParams }, JsonRequestBehavior.AllowGet);
        }

        
@{
    Layout = null;
}
<!DOCTYPE html>

<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>jqueryDatatable</title>
    <link href="https://cdn.datatables.net/1.13.6/css/jquery.dataTables.min.css" rel="stylesheet" />    
</head>
<body>
    <div> 
        <h2>Delay List</h2>
        <table id="employeeTable" class="display">
            <thead>
                <tr>
                    <th>SHIFT</th>
                    <th>FURNACE</th>
                    <th>TYPE</th>
                    <th>DELAY</th>
                    <th>START</th>
                    <th>END</th>
                </tr>
            </thead>
        </table>
    </div>
</body>
</html>
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<script src="https://cdn.datatables.net/1.13.6/js/jquery.dataTables.min.js"></script>

<script>
    $(document).ready(function () {
        debugger;
        $('#employeeTable').DataTable({            
            colResize:{
            realtime:true
            },
            "ajax": {
                "url": "@Url.Action("Furnace_Delay", "Sinter_Plant")",
                "type": "GET",
                "datatype": "json"
            },
            "columns": [
                { "data": "SHIFT" },
                { "data": "FURNACE" },
                { "data": "TYPE" },
                { "data": "DELAY" },
                { "data": "START_TIME" },
                { "data": "END_TIME" }
            ]
        });
    });
</script>
