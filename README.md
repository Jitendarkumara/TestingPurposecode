@model IEnumerable<TSMKhopoliTubeMillDashboard.Models.TubeMILL_1dashbord>

@{
    ViewData["Title"] = "Monitoring Dashboard";
}

<h2 style="text-align:center; color: #333; font-family: Arial, sans-serif;">Monitoring Dashboard</h2>

<div style="display: flex; flex-wrap: wrap; justify-content: center; gap: 20px;">
    <canvas id="powerChart" style="max-width: 500px; max-height: 300px;"></canvas>
    <canvas id="speedChart" style="max-width: 500px; max-height: 300px;"></canvas>
    <canvas id="frequencyChart" style="max-width: 500px; max-height: 300px;"></canvas>
    <canvas id="voltageCurrentChart" style="max-width: 500px; max-height: 300px;"></canvas>
</div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script>
    // Retrieve data from Model
    var sensorData = @Html.Raw(Json.Serialize(Model));
    console.log("Sensor Data:", sensorData);

    // Ensure timestamps are properly formatted for Chart.js
    var timestamps = sensorData.map(d => new Date(d.timestamp).toLocaleTimeString());

    // Extract data fields
    var power = sensorData.map(d => d.power);
    var speed = sensorData.map(d => d.lineSpeed);  // Ensure case matches Model
    var frequency = sensorData.map(d => d.actual_Frequency);
    var voltage = sensorData.map(d => d.voltage_Percentage);
    var current = sensorData.map(d => d.current_Percentage);

    function createChart(ctx, label, data, color) {
        return new Chart(ctx, {
            type: 'line',
            data: {
                labels: timestamps,
                datasets: [{
                    label: label,
                    data: data,
                    borderColor: color,
                    backgroundColor: color + "33",  // Transparent fill
                    fill: true,
                    tension: 0.3,  // Smooth curve
                    pointRadius: 3
                }]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                plugins: {
                    legend: { display: true },
                    tooltip: { enabled: true }
                },
                scales: {
                    x: {
                        display: true,
                        title: { display: true, text: 'Time', color: '#555' }
                    },
                    y: {
                        display: true,
                        title: { display: true, text: label, color: '#555' }
                    }
                }
            }
        });
    }

    // Create charts with better colors and formatting
    createChart(document.getElementById('powerChart'), 'Power', power, 'orange');
    createChart(document.getElementById('speedChart'), 'Speed', speed, 'red');
    createChart(document.getElementById('frequencyChart'), 'Frequency', frequency, 'blue');
    
    var voltageCurrentChart = new Chart(document.getElementById('voltageCurrentChart'), {
        type: 'line',
        data: {
            labels: timestamps,
            datasets: [
                {
                    label: 'Voltage %',
                    data: voltage,
                    borderColor: 'purple',
                    backgroundColor: 'purple33',
                    fill: true,
                    tension: 0.3
                },
                {
                    label: 'Current %',
                    data: current,
                    borderColor: 'green',
                    backgroundColor: 'green33',
                    fill: true,
                    tension: 0.3
                }
            ]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            plugins: { legend: { display: true }, tooltip: { enabled: true } },
            scales: {
                x: { display: true, title: { display: true, text: 'Time', color: '#555' } },
                y: { display: true, title: { display: true, text: 'Voltage & Current', color: '#555' } }
            }
        }
    });
</script>