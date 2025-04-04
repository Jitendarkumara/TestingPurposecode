<!DOCTYPE html><html lang="en">
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
        }.chart-wrapper {
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
    <div class="card shadow-lg p-3 rounded-4" style="background: linear-gradient(90deg, #e3f2fd, #4caf50); border: none; margin-top:5px ;margin-bottom:10px">
        <div class="card-header text-white text-center py-2 rounded-3" style="background: linear-gradient(135deg, #1976d2, #2196f3); font-size: 1.2rem; font-weight: 600;">
            Filter Options
        </div>
        <div class="card-body">
            <div class="row g-3">
                <div class="col-md-2">
                    <label for="FromDate" class="fw-bold">Select Date</label>
                    <input type="date" id="FromDate" class="form-control m-0">
                </div>
                <div class="col-md-2">
                    <label for="ToDate" class="fw-bold">Select Date</label>
                    <input type="date" id="ToDate" class="form-control m-0">
                </div>
                <div class="col-md-2">
                    <label for="shiftSelect" class="fw-bold">Select Shift</label>
                    <select id="shiftSelect" class="form-select m-0">
                        <option value="All">All</option>
                        <option value="A">Shift A</option>
                        <option value="B">Shift B</option>
                        <option value="C">Shift C</option>
                    </select>
                </div>
                <div class="col-md-2">
                    <label for="statusSelect" class="fw-bold">Select Status</label>
                    <select id="statusSelect" class="form-select m-0">
                        <option value="All">All</option>
                        <option value="1">Running</option>
                        <option value="0">Stop</option>
                    </select>
                </div>
                <div class="col-md-2">
                    <button id="btnSearch">Search</button>
                </div>
            </div>
        </div>
    </div>
    <div id="charts-container"></div>
    <script>
        const mills = ["Finishing-2", "Ext-coating", "Finishing-1", "Mill2", "Mill1"];
        let charts = {}; async function loadChartData(millName) {
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

    document.getElementById("btnSearch").addEventListener("click", function () {
        document.getElementById("charts-container").innerHTML = "";
        mills.forEach(mill => {
            let chartDiv = document.createElement("div");
            chartDiv.classList.add("chart-wrapper");
            chartDiv.innerHTML = `<div class="chart-title">${mill}</div><canvas id="chart-${mill}"></canvas>`;
            document.getElementById("charts-container").appendChild(chartDiv);
            loadChartData(mill);
        });
    });
</script>

</body>
</html>