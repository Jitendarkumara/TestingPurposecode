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
            const response = await fetch("/Home/GetKWHData");
            const data = await response.json();

            console.log("Fetched Data:", data); // ✅ Debugging log

            if (!data || data.length === 0) {
                alert("No data available!"); // ✅ Alert if no data is returned
                return;
            }

            // Extracting Mill Names & KWH data
            const millNames = data.map(item => item.millname.trim()); // ✅ Ensure correct key
            const kwhData = data.map(item => parseFloat(item.kwh)); // ✅ Ensure numerical values

            console.log("Mill Names:", millNames);
            console.log("KWH Data:", kwhData);

            // ✅ Correct LSL & USL Arrays
            const lslData = [9.647673215, 5, 8.169394883, 5, 31.04546611]; 
            const uslData = [42.71899345, 25, 50.31060512, 40, 180.2045339];

            // Create chart
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
                    aspectRatio: 2,
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
                        y: { beginAtZero: true, grid: { color: "#ddd" }, title: { display: true, text: "KWH Value" } },
                        x: { grid: { color: "#eee" }, title: { display: true, text: "Mill Names" } }
                    }
                }
            });
        }

        // Load chart on page load
        loadChartData();
    </script>
</body>
</html>