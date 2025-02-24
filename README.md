 public IActionResult GetKWHData()
 { MachineSectionDal M = new MachineSectionDal();
   
     List<Chartdata>  Data = new List<Chartdata>();
     DataTable dt = new DataTable();
     dt = M.getChartdata();
     for (int i = 0; i < dt.Rows.Count; i++)
     {
         Chartdata chartdata = new Chartdata();
         chartdata.TimeStamp = Convert.ToDateTime(dt.Rows[i][0].ToString());
         chartdata.Millname=(dt.Rows[i][1].ToString());
         chartdata.KWH = (Convert.ToDouble(dt.Rows[i][2]));
         Data.Add(chartdata);
     }
     return Json(Data);
 }

 @model List<EmsApplication.Models.Chartdata>

<!DOCTYPE html>
<html lang="en">
<head>
    <title>KWH Chart</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
        }

        #chart-container {
            width: 800px;
            height: 500px;
            margin: auto;
            padding: 20px;
            background-color: #f4f4f4;
            border-radius: 10px;
            box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);
        }

        canvas {
            width: 100% !important;
            height: 100% !important;
        }

        select {
            margin: 20px;
            padding: 10px;
            font-size: 16px;
        }
    </style>
</head>
<body>
    <h2>KWH Consumption Chart</h2>

    <label for="millSelect">Select Mill:</label>
    <select id="millSelect">
        <option value="">All Mills</option>
        @foreach (var mill in Model)
        {
            <option value="@mill.Millname">@mill.Millname</option>
        }
    </select>

    <div id="chart-container">
        <canvas id="kwhChart"></canvas>
    </div>

    <script>
        let kwhChart; // Store the chart instance

        async function loadChartData(selectedMill = null) {
            try {
                let url = "/Home/GetKWHData"; // API endpoint

                const response = await fetch(url);
                const data = await response.json();

                console.log("Fetched Data:", data);

                if (!data || data.length === 0) {
                    alert("No data available!");
                    return;
                }

                // Filter data if a specific mill is selected
                let filteredData = selectedMill ? data.filter(item => item.millname.trim() === selectedMill) : data;

                if (filteredData.length === 0) {
                    alert("No data found for the selected mill!");
                    return;
                }

                // Extract timestamps and KWH values
                const timestamps = filteredData.map(item => new Date(item.timestampLT).toLocaleString()); // Convert to readable format
                const kwhData = filteredData.map(item => parseFloat(item.kwh));

                console.log("Timestamps:", timestamps);
                console.log("KWH Data:", kwhData);

                // Define LSL & USL values
                const lslData = new Array(timestamps.length).fill(10); // Example LSL
                const uslData = new Array(timestamps.length).fill(50); // Example USL

                let ctx = document.getElementById("kwhChart").getContext("2d");

                // If chart already exists, update it
                if (kwhChart) {
                    kwhChart.data.labels = timestamps;
                    kwhChart.data.datasets[0].data = kwhData;
                    kwhChart.update();
                } else {
                    // Create a new chart
                    kwhChart = new Chart(ctx, {
                        type: "line",
                        data: {
                            labels: timestamps, // Use timestamps on X-axis
                            datasets: [
                                {
                                    label: "KWH",
                                    data: kwhData,
                                    borderColor: "blue",
                                    backgroundColor: "rgba(0, 123, 255, 0.3)",
                                    fill: true
                                },
                                {
                                    label: "LSL",
                                    data: lslData,
                                    borderColor: "orange",
                                    borderDash: [5, 5],
                                    fill: false
                                },
                                {
                                    label: "USL",
                                    data: uslData,
                                    borderColor: "gray",
                                    borderDash: [5, 5],
                                    fill: false
                                }
                            ]
                        },
                        options: {
                            responsive: true,
                            maintainAspectRatio: false,
                            aspectRatio: 2,
                            plugins: {
                                tooltip: {
                                    callbacks: {
                                        label: function (tooltipItem) {
                                            let datasetLabel = tooltipItem.dataset.label || "";
                                            let value = tooltipItem.raw;
                                            let timestamp = timestamps[tooltipItem.dataIndex];
                                            return `${timestamp} - ${datasetLabel}: ${value}`;
                                        }
                                    }
                                }
                            },
                            scales: {
                                y: {
                                    beginAtZero: true,
                                    grid: { color: "#ddd" },
                                    title: { display: true, text: "KWH Value" }
                                },
                                x: {
                                    grid: { color: "#eee" },
                                    title: { display: true, text: "Timestamp" },
                                    ticks: {
                                        autoSkip: true,
                                        maxTicksLimit: 10 // Avoid cluttering the x-axis
                                    }
                                }
                            }
                        }
                    });
                }
            } catch (error) {
                console.error("Error loading chart data:", error);
                alert("Failed to load data.");
            }
        }

        // Load chart on page load
        loadChartData();

        // Update chart on dropdown change
        document.getElementById("millSelect").addEventListener("change", function () {
            let selectedMill = this.value || null;
            loadChartData(selectedMill);
        });
    </script>
</body>
</html>
