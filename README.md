@model IEnumerable<TSMKhopoliTubeMillDashboard.Models.TubeMILL_1dashbord>

@{
    ViewData["Title"] = "Monitoring Dashboard";
}

<h2>Monitoring Dashboard</h2>

<canvas id="powerChart"></canvas>
<canvas id="speedChart"></canvas>
<canvas id="frequencyChart"></canvas>
<canvas id="voltageCurrentChart"></canvas>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script>
    var sensorData = @Html.Raw(Json.Serialize(Model));
    console.log(sensorData);
    var timestamps = sensorData.map(d => d.timestamp);
    var power = sensorData.map(d => d.power);
    var speed = sensorData.map(d => d.LineSpeed);
    var frequency = sensorData.map(d => d.Actual_Frequency);
    var voltage = sensorData.map(d => d.Voltage_Percentage);
    var current = sensorData.map(d => d.Current_Percentage);

    function createChart(ctx, label, data, color) {
        return new Chart(ctx, {
            type: 'line',
            data: {
                labels: timestamps,
                datasets: [{
                    label: label,
                    data: data,
                    borderColor: color,
                    fill: false
                }]
            },
            options: {
                responsive: true,
                scales: {
                    x: { display: true },
                    y: { display: true }
                }
            }
        });
    }

    createChart(document.getElementById('powerChart'), 'Power', power, 'orange');
    createChart(document.getElementById('speedChart'), 'Speed', speed, 'red');
    createChart(document.getElementById('frequencyChart'), 'Frequency', frequency, 'blue');
    createChart(document.getElementById('voltageCurrentChart'), 'Voltage & Current', voltage, 'purple');
</script>
