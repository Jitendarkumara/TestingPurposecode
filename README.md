<!DOCTYPE html>
<html lang="en">
<head>
    <title>KWH Chart</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        /* Set the size of the chart container */
        #chart-container {
            width: 600px;  /* Adjust width as needed */
            height: 400px; /* Adjust height as needed */
            margin: auto;  /* Center the chart */
        }

        /* Ensure the canvas does not stretch beyond its container */
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
            // Fetch KWH data from the controller action
            const response = await fetch("/Home/GetKWHData");
            const kwhData = await response.json();

            // Fixed LSL and USL values
            const lslData = [9.64, 5, 8.16, 5, 31.04];  
            const uslData = [42.71, 25, 50.31, 40, 180.20]; 

            // X-axis labels
            const labels = ["1", "2", "3", "4", "5"];

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
                    maintainAspectRatio: false, /* Prevent auto-resizing */
                    scales: {
                        y: { beginAtZero: true }
                    }
                }
            });
        }

        // Load chart data when page loads
        loadChartData();
    </script>
</body>
</html>