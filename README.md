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
            TimeStamp = Convert.ToDateTime(dt.Rows[i]["Timestamp"]),
            Millname = dt.Rows[i]["Mill_Id"].ToString(),
            KWH = Convert.ToDouble(dt.Rows[i]["TotalActvalue"])
        };

        Data.Add(chartdata);
        distinctMills.Add(chartdata.Millname); // Add unique mills
    }

    return Json(new { chartData = Data, mills = distinctMills.ToList() }); // Send chart data & unique mills
}

@model List<EmsApplication.Models.Chartdata>

<!DOCTYPE html>
<html lang="en">
<head>
    <title>KWH Chart</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/moment@2.29.1"></script>
    <script src="https://cdn.jsdelivr.net/npm/chartjs-adapter-moment"></script>



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

                // Ensure timestamps are properly formatted as JavaScript Date objects
                const timestamps = filteredData.map(item => new Date(item.timeStamp)); // Convert to Date object
                const kwhData = filteredData.map(item => parseFloat(item.kwh));

                console.log("Parsed Timestamps:", timestamps);
                console.log("KWH Data:", kwhData);

                let ctx = document.getElementById("kwhChart").getContext("2d");

                // Destroy existing chart instance to prevent duplication
                if (kwhChart) {
                    kwhChart.destroy();
                }

                // Create a new chart
                kwhChart = new Chart(ctx, {
                    type: "line",
                    data: {
                        labels: timestamps, // X-axis values
                        datasets: [
                            {
                                label: "KWH",
                                data: kwhData,
                                borderColor: "blue",
                                backgroundColor: "rgba(0, 123, 255, 0.3)",
                                fill: true
                            }
                        ]
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        scales: {
                            x: {
                                type: "time", // Set x-axis as time-based
                                time: {
                                    unit: "hour", // Change to 'day', 'minute', etc., if needed
                                   // tooltipFormat: "MMM d, yyyy HH:mm", // Format for tooltips
                                    displayFormats: {
                                        hour: "MMM d, HH:mm" // Format for x-axis labels
                                    }
                                },
                                title: {
                                    display: true,
                                    text: "Timestamp"
                                },
                                ticks: {
                                    autoSkip: true,
                                    maxTicksLimit: 10 // Avoids cluttering x-axis
                                }
                            },
                            y: {
                                beginAtZero: true,
                                title: {
                                    display: true,
                                    text: "KWH Value"
                                }
                            }
                        }
                    }
                });
            } catch (error) {
                console.error("Error loading chart data:", error);
                alert("Failed to load data.");
            }
        }

        // Load chart on page load
        loadChartData();

        // Update chart when dropdown changes
        document.getElementById("millSelect").addEventListener("change", function () {
            let selectedMill = this.value || null;
            loadChartData(selectedMill);
        });
    </script>
</body>
</html>
