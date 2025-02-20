<!DOCTYPE html>
<html lang="en">
<head>
    <title>KWH Chart</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        #chart-container {
            width: 800px; /* Adjust size */
            height: 500px; /* Adjust size */
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
    </style>
</head>
<body>
    <div id="chart-container">
        <canvas id="kwhChart"></canvas>
    </div>

    <script>
        async function loadChartData() {
            // Fetch data from controller
            const response = await fetch("/Home/GetKWHData");
            const data = await response.json();
            console.log(data); // Check if data is coming

            // Extract Mill Names & KWH values
            const millNames = data.map(item => item.MillName); // Ensure correct property name
            const kwhData = data.map(item => item.KWH);

            // Fixed LSL & USL values
            const lslData = new Array(millNames.length).fill(10); // Example LSL
            const uslData = new Array(millNames.length).fill(50); // Example USL

            var ctx = document.getElementById("kwhChart").getContext("2d");

            new Chart(ctx, {
                type: "line",
                data: {
                    labels: millNames,
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
                    aspectRatio: 2, // Adjust aspect ratio for better display
                    plugins: {
                        tooltip: {
                            callbacks: {
                                label: function (tooltipItem) {
                                    let datasetLabel = tooltipItem.dataset.label || "";
                                    let value = tooltipItem.raw;
                                    let millName = millNames[tooltipItem.dataIndex];
                                    return `${millName} - ${datasetLabel}: ${value}`;
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
                            title: { display: true, text: "Mill Names" }
                        }
                    }
                }
            });
        }

        // Load chart when page loads
        loadChartData();
    </script>
</body>
</html>