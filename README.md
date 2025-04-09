@model FeedersChartViewModel

@{
    ViewData["Title"] = "Feeders Chart";
}

<h2>Feeders Chart for @Model.Mill</h2>
<div id="charts-container"></div>

<!-- Scripts -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdn.jsdelivr.net/npm/chartjs-adapter-date-fns"></script>
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>

<script>
    const mill = '@Model.Mill';
    const fromDate = '@Model.FromDate';
    const toDate = '@Model.ToDate';
    const status = '@Model.Status';

    const charts = {};

    async function loadFeederCharts(selectedMill) {
        try {
            const feederResponse = await fetch(`/Home/GetFeeder?millName=${encodeURIComponent(selectedMill)}`);
            const feederList = await feederResponse.json();

            if (!feederList || feederList.length === 0) {
                console.warn("No feeders found.");
                return;
            }

            document.getElementById("charts-container").innerHTML = "";

            feederList.forEach(async feeder => {
                const feederName = feeder.feederName;
                if (!feederName) return;

                const chartId = `chart-${feederName.replace(/\s+/g, '-')}`;

                const chartDiv = document.createElement("div");
                chartDiv.classList.add("chart-wrapper");
                chartDiv.innerHTML = `
                    <h3 class="chart-title">${feederName}</h3>
                    <canvas id="${chartId}"></canvas>
                `;
                document.getElementById("charts-container").appendChild(chartDiv);

                const chartResponse = await fetch(`/Home/GetFeedersKWHData?millName=${encodeURIComponent(mill)}&Feeders=${encodeURIComponent(feederName)}&FromDate=${encodeURIComponent(fromDate)}&ToDate=${encodeURIComponent(toDate)}&RunningStatus=${encodeURIComponent(status)}`);
                const chartData = await chartResponse.json();

                if (!chartData || chartData.length === 0) return;

                const timestamps = chartData.map(item => new Date(item.timeStamp));
                const kwhData = chartData.map(item => parseFloat(item.kwh));
                const lslData = chartData.map(item => parseFloat(item.lsl));
                const uslData = chartData.map(item => parseFloat(item.usl));

                const ctx = document.getElementById(chartId).getContext("2d");

                if (charts[feederName]) charts[feederName].destroy();

                charts[feederName] = new Chart(ctx, {
                    type: "line",
                    data: {
                        labels: timestamps,
                        datasets: [
                            {
                                label: "KWH",
                                data: kwhData,
                                borderColor: "blue",
                                backgroundColor: "rgba(0, 0, 255, 0.1)",
                                fill: false
                            },
                            {
                                label: "LSL",
                                data: lslData,
                                borderColor: "green",
                                borderDash: [5, 5],
                                fill: false
                            },
                            {
                                label: "USL",
                                data: uslData,
                                borderColor: "red",
                                borderDash: [5, 5],
                                fill: false
                            }
                        ]
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        plugins: {
                            legend: { position: 'top' },
                            title: { display: false }
                        },
                        scales: {
                            x: {
                                type: 'time',
                                time: {
                                    unit: 'minute',
                                    tooltipFormat: 'MMM d, HH:mm'
                                },
                                title: {
                                    display: true,
                                    text: 'Timestamp'
                                }
                            },
                            y: {
                                title: {
                                    display: true,
                                    text: 'KWH / LSL / USL'
                                }
                            }
                        }
                    }
                });
            });
        } catch (error) {
            console.error("Error loading feeder charts:", error);
        }
    }

    window.onload = function () {
        loadFeederCharts(mill);
    };
</script>

<style>
    #charts-container {
        display: flex;
        flex-wrap: wrap;
        gap: 20px;
        justify-content: center;
    }

    .chart-wrapper {
        position: relative;
        width: 100%;
        max-width: 800px;
        height: 400px;
        border: 2px solid black;
        padding: 10px;
        background-color: white;
        box-shadow: 2px 2px 8px rgba(0, 0, 0, 0.1);
        border-radius: 8px;
    }

    .chart-wrapper canvas {
        width: 100% !important;
        height: 100% !important;
    }

    .chart-title {
        text-align: center;
        font-weight: bold;
        margin-bottom: 10px;
    }
</style>