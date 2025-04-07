@model FeedersChartViewModel

@{
    ViewData["Title"] = "Feeders Chart";
}

<h2>Feeders Chart for @Model.Mill</h2>
<canvas id="feedersChart" width="800" height="400"></canvas>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>

<script>
    const mill = '@Model.Mill';
    const fromDate = '@Model.FromDate';
    const toDate = '@Model.ToDate';
    const status = '@Model.Status';

    $.ajax({
        url: '/Feeders/GetFeedersData',
        method: 'GET',
        data: {
            mill: mill,
            from: fromDate,
            to: toDate,
            status: status
        },
        success: function (data) {
            if (!data || data.length === 0) {
                alert("No feeder data found.");
                return;
            }

            const timestamps = data.map(item => new Date(item.timestamp));
            const kwhData = data.map(item => item.kwh);
            const lslData = data.map(item => item.lsl);
            const uslData = data.map(item => item.usl);

            const ctx = document.getElementById('feedersChart').getContext('2d');
            new Chart(ctx, {
                type: 'line',
                data: {
                    labels: timestamps,
                    datasets: [
                        { label: 'KWH', data: kwhData, borderColor: 'blue', fill: false },
                        { label: 'LSL', data: lslData, borderColor: 'green', borderDash: [5, 5] },
                        { label: 'USL', data: uslData, borderColor: 'red', borderDash: [5, 5] }
                    ]
                },
                options: {
                    responsive: true,
                    scales: {
                        x: { type: 'time', time: { unit: 'minute' } },
                        y: { beginAtZero: true }
                    }
                }
            });
        },
        error: function (err) {
            console.error("Error fetching feeders data", err);
        }
    });
</script>