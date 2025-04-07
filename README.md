@model FeedersChartViewModel

@{
    ViewData["Title"] = "Feeders Chart";
}

<h2>Feeders Chart for @Model.Mill</h2>
<canvas id="feedersChart" width="800" height="400"></canvas>

<div id="charts-container"></div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>

<script>
    const mill = '@Model.Mill';
    const fromDate = '@Model.FromDate';
    const toDate = '@Model.ToDate';
    const status = '@Model.Status';
    alert(mill);
    async function loadFeedername(selectedMill) {
            try {
                let url = `/Home/GetFeeder?millName=${encodeURIComponent(selectedMill)}`;
                const response = await fetch(url);
                const data = await response.json();
                if (!data || data.length === 0) return;

                
                data.forEach(feeder => {
                console.log(feeder);
                      let url = `/Home/GetFeedersKWHData?millName=${encodeURIComponent(mill)}&Feeders=${encodeURIComponent(feeder)}&FromDate=${encodeURIComponent(fromDate)}&ToDate=${encodeURIComponent(toDate)}&RunningStatus=${encodeURIComponent(selectedStatus)}`;
                      const response = await fetch(url);

                let timestamps = data.map(item => new Date(item.timeStamp));
                let kwhData = data.map(item => parseFloat(item.kwh));
                let lslData = data.map(item => parseFloat(item.lsl));
                let uslData = data.map(item => parseFloat(item.usl));

                let ctx = document.getElementById(`chart-${millName}`).getContext("2d");

                if (charts[millName]) {
                    charts[millName].destroy();
                }

                charts[millName] = new Chart(ctx, {
                    type: "line",
                    data: {
                        labels: timestamps,
                        datasets: [
                            { label: "KWH", data: kwhData, borderColor: "blue", fill: false },
                            { label: "LSL", data: lslData, borderColor: "green", borderDash: [5, 5] },
                            { label: "USL", data: uslData, borderColor: "red", borderDash: [5, 5] }
                        ]
                    },
                    options: {
                        responsive: true,
                        scales: {
                            x: { type: "time", time: { unit: "minute" }, title: { display: true, text: "Timestamp" } },
                            y: { title: { display: true, text: "KWH / LSL / USL" } }
                        }
                    }
                });
                });

               
                loadChartData(selectedMill, selectedFeeder, selectedDate,Runningstatus);
            } catch (error) {
                console.error("Error loading Feeder Names:", error);
            }
        }
         function renderAllCharts() {
            document.getElementById("charts-container").innerHTML = "";

            mills.forEach(mill => {
                let chartDiv = document.createElement("div");
                chartDiv.classList.add("chart-wrapper");
                chartDiv.innerHTML = `
                    <div class="chart-title">${mill}</div>
                    <button class="Feeders-btn" data-mill="${mill}">Feeders</button>
                    <canvas id="chart-${mill}"></canvas>
                `;
                document.getElementById("charts-container").appendChild(chartDiv);
                loadChartData(mill);
            });
            }
         window.onload = function () {
              const mill = '@Model.Mill';
            loadFeedername(mill)
        };
</script>
