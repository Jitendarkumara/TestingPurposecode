<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<script>
    const mills = ["Finishing-2", "Ext-coating", "Finishing-1", "Mill2", "Mill1"];
    let charts = {};

    async function loadChartData(millName) {
        try {
            let FromDate = document.getElementById("FromDate").value;
            let ToDate = document.getElementById("ToDate").value;
            let selectedShift = document.getElementById("shiftSelect").value;
            let selectedStatus = document.getElementById("statusSelect").value;

            if (!FromDate) {
                alert("Please select a date.");
                return;
            }

            let url = `/Home/GetKWHData?millName=${encodeURIComponent(millName)}&FromDate=${encodeURIComponent(FromDate)}&ToDate=${encodeURIComponent(ToDate)}&shiftSelect=${encodeURIComponent(selectedShift)}&RunningStatus=${encodeURIComponent(selectedStatus)}`;

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

        } catch (error) {
            console.error(`Error loading data for ${millName}:`, error);
        }
    }

    function renderAllCharts() {
        document.getElementById("charts-container").innerHTML = "";
        mills.forEach(mill => {
            let chartDiv = document.createElement("div");
            chartDiv.classList.add("chart-wrapper");
            chartDiv.innerHTML = `<div class="chart-title">${mill}</div><canvas id="chart-${mill}"></canvas>`;
            document.getElementById("charts-container").appendChild(chartDiv);
            loadChartData(mill);
        });
    }

    // Event listener for the Search button
    document.getElementById("btnSearch").addEventListener("click", renderAllCharts);

    // On page load
    $(document).ready(function () {
        const today = new Date().toISOString().split("T")[0];
        $("#FromDate").val(today);
        $("#ToDate").val(today);
        renderAllCharts();
    });
</script>