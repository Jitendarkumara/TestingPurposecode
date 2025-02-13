		<div class="charts-container">
			<!-- First Row of Charts -->
			<div class="charts-row">
				<div class="chart-container">
					<div style="display:flex;align-items:center;gap:10px">
					<h3>Power</h3>
					<div class="ErrorIndicator power" data-value="">P</div>
				</div>
					<canvas id="powerChart"></canvas>
				</div>
				<div class="chart-container">
				<div style="display:flex;align-items:center;gap:10px">
						<center>
							<h3>Frequency</h3>	<div class="ErrorIndicator freq" data-value="">F</div>
					</center>
					</div>
					<canvas id="frequencyChart"></canvas>
				</div>
			</div>
			<!-- Second Row of Charts -->
			<div class="charts-row">
				<div class="chart-container">
					<div style="display:flex;align-items:center;gap:10px">
					<h3>Speed </h3>	<div class="ErrorIndicator speed" data-value="">S</div>
					</div>
					<canvas id="speedChart"></canvas>
				</div>
				<div class="chart-container">
					<div style="display:flex;align-items:center;gap:10px">
					<h3>Voltage and Current</h3>	<div class="ErrorIndicator voltage" data-value="">V</div> <div class="ErrorIndicator current" data-value="">C</div>
					</div>
					<canvas id="voltageCurrentChart"></canvas>
				</div>
			</div>
		</div>
// Function to update all charts
function updateCharts(filteredData) {
	// Update line charts
	powerChart.data.labels = filteredData.timestamps;
	powerChart.data.datasets[0].data = filteredData.power;
	powerChart.update();

	frequencyChart.data.labels = filteredData.timestamps;
	frequencyChart.data.datasets[0].data = filteredData.frequency;
	frequencyChart.update();

	speedChart.data.labels = filteredData.timestamps;
	speedChart.data.datasets[0].data = filteredData.speed;
	speedChart.update();

	voltageCurrentChart.data.labels = filteredData.timestamps;
	voltageCurrentChart.data.datasets[0].data = filteredData.voltage;
	voltageCurrentChart.data.datasets[1].data = filteredData.current;
	voltageCurrentChart.update();
}
