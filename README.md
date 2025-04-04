<div style="position: relative; width: 400px; height: 300px;">
    <canvas id="myChart"></canvas>
    <button id="chartButton" style="position: absolute; top: 10px; right: 10px; z-index: 10;">Click</button>
</div>

<script>
    const ctx = document.getElementById('myChart').getContext('2d');
    new Chart(ctx, {
        type: 'bar',
        data: {
            labels: ['A', 'B', 'C'],
            datasets: [{ data: [10, 20, 30] }]
        }
    });
</script>