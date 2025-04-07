<!DOCTYPE html>
<html lang="en">
<head>
    <title>Submill Chart</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/moment@2.29.1"></script>
    <script src="https://cdn.jsdelivr.net/npm/chartjs-adapter-moment"></script>
</head>
<body>
    <h2 id="title"></h2>
    <canvas id="submillChart" width="800" height="400"></canvas>

    <script>
        function getQueryParams() {
            const params = new URLSearchParams(window.location.search);
            return {
                mill: params.get("mill"),
                from: params.get("from"),
                to: params.get("to"),
                shift: params.get("shift"),
                status: params.get("status")
            };
        }

        async function loadSubmillChartData() {
            const { mill, from, to, shift, status } = getQueryParams();
            document.getElementById("title").innerText = `Submill Chart for ${mill}`;

            const url = `/Home/GetSubmillData?millName=${encodeURIComponent(mill)}&FromDate=${from}&ToDate=${to}&shiftSelect=${shift}&RunningStatus=${status}`;
            const response = await fetch(url);
            const data = await response.json();

            const ctx = document.getElementById("submillChart").getContext("2d");
            const timestamps = data.map(d => new Date(d.timeStamp));
            const values = data.map(d => parseFloat(d.kwh));

            new Chart(ctx, {
                type: 'line',
                data: {
                    labels: timestamps,
                    datasets: [{
                        label: `${mill} Submill KWH`,
                        data: values,
                        borderColor: 'purple',
                        fill: false
                    }]
                },
                options: {
                    scales: {
                        x: { type: 'time', time: { unit: 'minute' }, title: { display: true, text: "Timestamp" } },
                        y: { title: { display: true, text: "KWH" } }
                    }
                }
            });
        }

        loadSubmillChartData();
    </script>
</body>
</html>