<!DOCTYPE html>
<html lang="en">
<head>
    <title>KWH Chart</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>
<body>
    <canvas id="kwhChart"></canvas>

    <script>
        async function loadChartData() {
            // Fetch KWH data from API
            const response = await fetch("/api/Chart/GetKWHData");
            const kwhData = await response.json();

            // Fixed LSL and USL values
            const lslData = [9.64, 5, 8.16, 5, 31.04];  // Your fixed values
            const uslData = [42.71, 25, 50.31, 40, 180.20]; // Your fixed values

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