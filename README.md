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

            // Extract values from JSON response
            const millNames = data.map(item => item.MillName);
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