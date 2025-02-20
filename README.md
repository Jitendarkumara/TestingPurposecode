SELECT 
    [timestampSourceLT], 
    CASE  
        WHEN [SourceID] = 64 THEN 'Finishing1'
        WHEN [SourceID] = 65 THEN 'Mill1' 
        WHEN [SourceID] = 68 THEN 'Mill2' 
        WHEN [SourceID] = 70 THEN 'Finishing2'
        WHEN [SourceID] = 75 THEN 'Extcoating' 
    END AS MillName, 
    ROUND([Value], 2) AS Value
FROM [ION_Data].[dbo].[View_DataLog2] 
WHERE SourceID IN (64, 65, 68, 70, 75) 
    AND QuantityID = 182
ORDER BY [timestampSourceLT] DESC, MillName;
<!DOCTYPE html>
<html lang="en">
<head>
    <title>KWH Chart</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        #chart-container {
            width: 600px;
            height: 400px;
            margin: auto;
        }

        canvas {
            width: 100% !important;
            height: 100% !important;
        }
    </style>
</head>
<body>
    <div id="chart-container">
        <canvas id="kwhChart"></canvas>
    </div>

    <script>
        async function loadChartData() {
            // Fetch KWH data and mill names from the controller
            const response = await fetch("/Home/GetKWHData");
            const data = await response.json();
            console.log(data);
            // Extract values from JSON response
            const millNames = data.map(item => item.Millname);
            const kwhData = data.map(item => item.KWH);

            // Fixed LSL and USL values
            const lslData = [9.64, 5, 8.16, 5, 31.04];
            const uslData = [42.71, 25, 50.31, 40, 180.20];

            // X-axis labels (Mill Names)
            const labels = millNames;

            // Chart.js configuration
            var ctx = document.getElementById("kwhChart").getContext("2d");
            new Chart(ctx, {
                type: "line",
                data: {
                    labels: labels,
                    datasets: [
                        {
                            label: "KWH",
                            data: kwhData,
                            borderColor: "blue",
                            fill: false
                        },
                        {
                            label: "LSL",
                            data: lslData,
                            borderColor: "orange",
                            fill: false
                        },
                        {
                            label: "USL",
                            data: uslData,
                            borderColor: "gray",
                            fill: false
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: { beginAtZero: true }
                    },
                    plugins: {
                        tooltip: {
                            callbacks: {
                                label: function (tooltipItem) {
                                    let datasetLabel = tooltipItem.dataset.label || "";
                                    let value = tooltipItem.raw;
                                    let millName = labels[tooltipItem.dataIndex]; // Fetch mill name dynamically
                                    return `${millName} - ${datasetLabel}: ${value}`;
                                }
                            }
                        }
                    }
                }
            });
        }

        // Load chart data when page loads
        loadChartData();
    </script>
</body>
</html>
public IActionResult GetKWHData()
{ MachineSectionDal M = new MachineSectionDal();
  
    List<Chartdata>  Data = new List<Chartdata>();
    DataTable dt = new DataTable();
    dt = M.getChartdata();
    for (int i = 0; i < dt.Rows.Count; i++)
    {
        Chartdata chartdata = new Chartdata();
        chartdata.Millname=(dt.Rows[i][1].ToString());
        chartdata.KWH = (Convert.ToDouble(dt.Rows[i][2]));
        Data.Add(chartdata);
    }
    return Json(Data);
}
