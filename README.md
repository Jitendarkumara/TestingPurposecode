 async function loadChartData(selectedMill = "Mill1", selectedFeeder = "All") {
     try {
         let selectedDate = document.getElementById("dateFilter").value; // Ensure selectedDate is defined
         console.log("Selected Date:", selectedDate);

         let url = `/Home/GetKWHData?millName=${encodeURIComponent(selectedMill)}&feederName=${encodeURIComponent(selectedFeeder)}&selectedDate=${encodeURIComponent(selectedDate)}`;

         const response = await fetch(url);
         if (!response.ok) throw new Error(`HTTP error! Status: ${response.status}`);

         const data = await response.json();
         console.log("Fetched Data:", data);

         if (!data || data.length === 0) return;

         let timestamps = data.map(item => new Date(item.timeStamp));
         let kwhData = data.map(item => parseFloat(item.kwh));
         console.log(kwhData);
         let ctx = document.getElementById("kwhChart").getContext("2d");

         if (kwhChart) {
             kwhChart.data.labels = timestamps;
             kwhChart.data.datasets[0].data = kwhData;
             kwhChart.update();
         } else {
             kwhChart = new Chart(ctx, {
                 type: "line",
                 data: {
                     labels: timestamps,
                     datasets: [
                         {
                             label: "KWH",
                             data: kwhData,
                             borderColor: "blue",
                             fill: true
                         }
                     ]
                 },
                 options: {
                     responsive: true,
                     scales: {
                         x: {
                             type: "time",
                             time: { unit: "minute" },
                             title: { display: true, text: "Timestamp" }
                         }
                     }
                 }
             });
         }
     } catch (error) {
         console.error("Error loading chart data:", error);
     }
 }

  public IActionResult GetKWHData(string MillName, string feederName,DateTime selectedDate)
 {
     List<Chartdata> Data = new List<Chartdata>();
     try
     {
         MachineSectionDal M = new MachineSectionDal();
         DataTable dt = M.getChartdata(MillName, feederName,selectedDate);

         for (int i = 0; i < dt.Rows.Count; i++)
         {
             Chartdata chartdata = new Chartdata();

             try
             {
                 chartdata.TimeStamp = dt.Rows[i]["TimeStamp"] != DBNull.Value
                     ? Convert.ToDateTime(dt.Rows[i]["TimeStamp"])
                     : DateTime.MinValue;

                 chartdata.Millname = dt.Rows[i]["Mill_Id"] != DBNull.Value
                     ? dt.Rows[i]["Mill_Id"].ToString()
                     : string.Empty;

                 chartdata.KWH = dt.Rows[i]["TotalActValue"] != DBNull.Value
                     ? Convert.ToDouble(dt.Rows[i]["TotalActValue"])
                     : 0.0; // Default value if NULL
                 chartdata.LSL = dt.Rows[i]["LSL"] != DBNull.Value
               ? Convert.ToDouble(dt.Rows[i]["LSL"])
               : 0.0; // Default value if NULL

                 chartdata.USL = dt.Rows[i]["USL"] != DBNull.Value
               ? Convert.ToDouble(dt.Rows[i]["USL"])
               : 0.0; // Default value if NULL

                 Data.Add(chartdata);
             }
             catch (Exception ex)
             {
                 // Log individual row conversion error (optional)
                 Console.WriteLine($"Error processing row {i}: {ex.Message}");
             }
         }
     }
     catch (Exception ex)
     {
         // Log the error and return an error response
         Console.WriteLine($"Error in GetKWHData: {ex.Message}");
         return StatusCode(500, new { message = "An error occurred while fetching data.", error = ex.Message });
     }

     return Json(Data);
 }
     
