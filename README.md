<!DOCTYPE html>
<html lang="en">
<head>
    <title>KWH Chart</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/moment@2.29.1"></script>
    <script src="https://cdn.jsdelivr.net/npm/chartjs-adapter-moment"></script>
    <script src="https://cdn.jsdelivr.net/npm/chartjs-plugin-zoom"></script>

    <style>
        #charts-container {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
            justify-content: center;
        }

        .chart-wrapper {
            position: relative;
            width: 600px;
            height: 300px;
            border: 2px solid black;
            padding: 10px;
            background-color: white;
            box-shadow: 2px 2px 8px rgba(0, 0, 0, 0.1);
            border-radius: 8px;
        }

        .chart-title {
            text-align: center;
            font-weight: bold;
            margin-bottom: 10px;
        }

        .chart-btn {
            position: absolute;
            top: 10px;
            right: 10px;
            z-index: 10;
            padding: 5px 10px;
            background-color: blue;
            color: white;
            border: none;
            cursor: pointer;
            border-radius: 5px;
        }

    </style>
</head>
<body>

    <div id="charts-container"></div>

    <script>
        const mills = ["Finishing-2", "Ext-coating", "Finishing-1", "Mill2", "Mill1"];
        let charts = {}; // Store chart instances

        async function loadChartData(millName) {
            try {
                let selectedDate = new Date().toISOString().split('T')[0];
                let url = `/Home/GetKWHData?millName=${encodeURIComponent(millName)}&selectedDate=${encodeURIComponent(selectedDate)}`;

                const response = await fetch(url);
                if (!response.ok) throw new Error(`HTTP error! Status: ${response.status}`);

                const data = await response.json();
                if (!data || data.length === 0) {
                    console.warn(`No data available for ${millName}`);
                    return;
                }

                let timestamps = data.map(item => new Date(item.timeStamp));
                let kwhData = data.map(item => parseFloat(item.kwh));
                let lslData = data.map(item => parseFloat(item.lsl));
                let uslData = data.map(item => parseFloat(item.usl));

                let ctx = document.getElementById(`chart-${millName}`).getContext("2d");

                // Destroy previous chart instance if exists
                if (charts[millName]) {
                    charts[millName].destroy();
                }

                // Create new chart
                charts[millName] = new Chart(ctx, {
                    type: "line",
                    data: {
                        labels: timestamps,
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
                                borderColor: "green",
                                borderDash: [5, 5]
                            },
                            {
                                label: "USL",
                                data: uslData,
                                borderColor: "red",
                                borderDash: [5, 5]
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
                            },
                            y: {
                                title: { display: true, text: "KWH / LSL / USL" }
                            }
                        }
                    }
                });

            } catch (error) {
                console.error(`Error loading data for ${millName}:`, error);
            }
        }

        function createChartContainer(millName) {
            const container = document.getElementById("charts-container");
            const chartDiv = document.createElement("div");
            chartDiv.classList.add("chart-wrapper");

            chartDiv.innerHTML = `
                <div class="chart-title">${millName}</div>
                <canvas id="chart-${millName}"></canvas>
                <button class="chart-btn" onclick="loadChartData('${millName}')">Reload</button>
            `;

            container.appendChild(chartDiv);
        }

        function initializeCharts() {
            mills.forEach(mill => {
                createChartContainer(mill);
                loadChartData(mill);
            });
        }

        window.onload = initializeCharts;
    </script>

</body>
</html>