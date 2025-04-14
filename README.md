@model TSMKhopoliTubeMillDashboard.Models.CombinedViewModel

@{
	ViewBag.Title = "Dashboard";
	Layout = null;
}

<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="utf-8" />
	<title>TSM Khopoli TubeMill Dashboard</title>
	<link rel="stylesheet" href="/css/Dashboard.css" />
	<link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css" />
</head>
<body>

	<div class="container">

		<!-- Table Selection Dropdown -->
		<div class="date-filter">
			<button type="button" id="logoutBtn" class="btn btn-warning shadow-sm  top-right-button fw-bold" onclick="redirectToLoginPage()">Log Out</button>
			<button type="button" class="btn btn-primary shadow-sm top-right-button-2  fw-bold" onclick="redirectToPage()">Go To TubeMill Dashboard Page</button>

			<form id="tableSelectForm" method="get" action="@Url.Action("Dashboard", "Dashboard")">
				<label for="tableSelect">Select TubeMill :</label>
				@* <select id="tableSelect" name="tableName" onchange="this.form.submit()"> *@
					<select id="tableSelect" name="tableName"  >
					@foreach (var table in ViewBag.AvailableTables)
					{
						<option value="@table" selected="@(table == ViewBag.SelectedTable ? "selected" : null)">
							@table
						</option>
					}
				</select>
			</form>

			<div class="filter-group">
				<label for="selectedDate">Select Date:</label>
				<input type="date" id="selectedDate" name="selectedDate">
				<label for="shiftSelect">Select Shift:</label>
				<select id="shiftSelect" name="shiftSelect">
					<option value="all">All_Shifts</option>
					<option value="A">A_Shift</option>
					<option value="B">B_Shift</option>
					<option value="C">C_Shift</option>
				</select>
				<button id="filterButton">Show Data</button>
			</div>
		</div>

		<div style="display: flex; align-items: center; gap: 10px;">
			<div class="ErrorIndicator" style="width: 20px; height: 20px; background-color: red; border-radius: 50%;"></div>
			<span>Red circle indicates parameters cause for defect</span>

			<div class="ErrorIndicator" style="width: 20px; height: 20px; background-color: green; border-radius: 50%;"></div>
			<span>Green circle indicates no defect</span>
		</div>
		
		<div class="modal fade" id="progressModal" tabindex="-1" aria-hidden="true">
			<div class="modal-dialog modal-dialog-centered">
				<div class="modal-content text-center p-4">
					<h5>Loading...</h5>
					<div class="spinner-border text-primary" role="status" style="width: 5rem; height: 5rem;">
						<span class="visually-hidden">Loading...</span>
					</div>
				</div>
			</div>
		</div>
		<!-- Charts Container -->
		<div class="charts-container">
			<!-- First Row of Charts -->
			<div class="charts-row">
				<div class="chart-container">
					<div style="display:flex;align-items:center;gap:10px">
					<h3>Power</h3>
					<div class="ErrorIndicator power" data-value=""></div>
				</div>
					<canvas id="powerChart"></canvas>
				</div>
				<div class="chart-container">
				<div style="display:flex;align-items:center;gap:10px">
						<center>
							<h3>Frequency</h3>	<div class="ErrorIndicator freq" data-value=""></div>
					</center>
					</div>
					<canvas id="frequencyChart"></canvas>
				</div>
			</div>
			<!-- Second Row of Charts -->
			<div class="charts-row">
				<div class="chart-container">
					<div style="display:flex;align-items:center;gap:10px">
					<h3>Speed </h3>	<div class="ErrorIndicator speed" data-value=""></div>
					</div>
					<canvas id="speedChart"></canvas>
				</div>
				<div class="chart-container">
					<div style="display:flex;align-items:center;gap:10px">
					<h3>Voltage and Current</h3>	<div class="ErrorIndicator voltage" data-value=""></div> <div class="ErrorIndicator current" data-value="">C</div>
					</div>
					<canvas id="voltageCurrentChart"></canvas>
				</div>
			</div>
		</div>

		<!-- Input Panel -->
	
		<form asp-action="SaveTubeInput" method="post">
			<div class="d-flex flex-wrap gap-2">
				<div class="form-group">
					<label for="inputDateTime">Date and Time:</label>
					<input type="datetime-local" id="inputDateTime" name="CreatedOn" required class="form-control" />
				</div>
				<div class="form-group">
					<label for="od">OD:</label>
					<input type="text" id="od" name="OD" placeholder="OD" required class="form-control" />
				</div>
				<div class="form-group">
					<label for="thickness">Thickness:</label>
					<input type="text" id="thickness" name="Thickness" placeholder="Thickness" required class="form-control" />
				</div>
				<div class="form-group">
					<label for="grade">Grade:</label>
					<input type="text" id="grade" name="Grade" placeholder="Grade" required class="form-control" />
				</div>
				<div class="form-group">
					<label for="customer">Customer:</label>
					<input type="text" id="customer" name="Customer" placeholder="Customer" required class="form-control" />
				</div>
				<div class="form-group">
					<label for="MillNo">MillNo:</label>
					<input type="text" id="MillNo" name="MillNo" placeholder="MillNo" required class="form-control" />
				</div>
			</div>
			<div class="mt-3">
				<center> <button type="submit" id="submitButton" class="btn btn-primary">Submit</button> </center>
			</div>
		</form>


		<h3>Detail View</h3>
		<table>
			<thead>
				<tr>
					<th>Timestamp</th>
					<th>Voltage (%)</th>
					<th>Current (%)</th>
					<th>Power (kW)</th>
					<th>Frequency (kHz)</th>
					<th>Speed (mpm)</th>
					<th>OD</th>
					@*        <th>Thickness</th> *@
					<th>Grade</th>
					@*  <th>Customer</th> *@
					<th>MillNo</th>
				@* 	<th>Alarm</th> *@
					<th>Error</th>
				</tr>
			</thead>

			<tbody id="dataTableBody">

			</tbody>
		</table>

	</div>

	<!-- Scripts section -->
	<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
	<script src="https://cdn.jsdelivr.net/npm/moment"></script>
	<script src="https://cdn.jsdelivr.net/npm/chartjs-adapter-moment"></script>
	 

	<script>
		let allData = {
			timestamps: @Html.Raw(Newtonsoft.Json.JsonConvert.SerializeObject(Model.TubeMills.Select(m => m.Timestamp).ToArray())),
			power: @Html.Raw(Newtonsoft.Json.JsonConvert.SerializeObject(Model.TubeMills.Select(m => m.Actual_kW).ToArray())),
			frequency: @Html.Raw(Newtonsoft.Json.JsonConvert.SerializeObject(Model.TubeMills.Select(m => m.Actual_Frequency).ToArray())),
			current: @Html.Raw(Newtonsoft.Json.JsonConvert.SerializeObject(Model.TubeMills.Select(m => m.Current_Percentage).ToArray())),
			voltage: @Html.Raw(Newtonsoft.Json.JsonConvert.SerializeObject(Model.TubeMills.Select(m => m.Voltage_Percentage).ToArray())),
			speed: @Html.Raw(Newtonsoft.Json.JsonConvert.SerializeObject(Model.TubeMills.Select(m => m.LineSpeed).ToArray()))
						};

		// Function to filter data based on date range
		function filterDataByDay(selectedDate) {
			const startOfDay = new Date(selectedDate);
			startOfDay.setHours(0, 0, 0, 0);

			const endOfDay = new Date(selectedDate);
			endOfDay.setHours(23, 59, 59, 999);

			const filteredIndices = allData.timestamps.map((timestamp, index) => {
				const date = new Date(timestamp);
				return (date >= startOfDay && date <= endOfDay) ? index : null;
			}).filter(index => index !== null);

			return {
				timestamps: filteredIndices.map(i => allData.timestamps[i]),
				power: filteredIndices.map(i => allData.power[i]),
				frequency: filteredIndices.map(i => allData.frequency[i]),
				current: filteredIndices.map(i => allData.current[i]),
				voltage: filteredIndices.map(i => allData.voltage[i]),
				speed: filteredIndices.map(i => allData.speed[i])
			};
		}

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

		document.getElementById('filterButton').addEventListener('click', () => {
			const selectedDate = document.getElementById('selectedDate').value;
						
			if (!selectedDate) {
				alert('Please select a date');
				return;
			}
			//---here is coding to recieve data after filtering date and mill 
			//var filteredData = Model.Alarm.Where(item => item.Mill == selectedMill && item.timestamp==selectedDate).ToList();

			getdatabyDateAndShift();

			const filteredData = filterDataByDay(selectedDate);

			if (filteredData.timestamps.length === 0) {
				alert('No data available for the selected date');
				return;
			}
			
			updateCharts(filteredData);
		});
		//------------------------here code written for data fetch according to shift
		function getdatabyDateAndShift() { 
					const selectedDate = document.getElementById('selectedDate').value; 
					const selectedShift = document.getElementById('shiftSelect').value; 
					const selectedMill = document.getElementById('tableSelect').value;

		if (!selectedDate || !selectedShift || !selectedMill) {
			alert('Please select a date, shift, and mill');
			return;
		}
	
		
	 

		  fetch(`/Dashboard/GetFilteredData?selectedDate=${selectedDate}&selectedShift=${selectedShift}&selectedMill=${selectedMill}`)
		.then(response => response.json())
		.then(data => {
			if (data.length === 0) {
				alert('No data available for the selected criteria');
				return;
			}
			
			// Update table content
			let tableBody = document.getElementById('dataTableBody');

			tableBody.innerHTML = ""; // Clear previous data
		
			data.forEach(item => {
								let dateObj = new Date(item.timestamp);

		//  Manually format to 24-hour format (YYYY-MM-DD HH:mm:ss)
		 let formattedTimestamp = String(dateObj.getDate()).padStart(2, '0') + "-" +
							 String(dateObj.getMonth() + 1).padStart(2, '0') + "-" +
							 dateObj.getFullYear() + " " +
							 String(dateObj.getHours()).padStart(2, '0') + ":" +
							 String(dateObj.getMinutes()).padStart(2, '0') + ":" +
							 String(dateObj.getSeconds()).padStart(2, '0');


				let row = `<tr class="DS">
							<td>${formattedTimestamp}</td>
							<td>${Math.round(item.voltage, 2)}</td>
							<td>${Math.round(item.currents, 2)}</td>
							<td>${Math.round(item.power, 2)}</td>
							<td>${Math.round(item.frequency, 2)}</td>
							<td>${Math.round(item.speed, 2)}</td>
							<td>${item.od}</td>
							<td>${item.grade}</td>
							<td class="DS-error" data-value="${item.alarm}">${item.mill}</td>
							<td data-value="${item.erroneous_Parameter}">${item.erroneous_Parameter}</td>
						</tr>`;
				tableBody.innerHTML += row;
			});
		 
			ErrorIndicator();
		})
		.catch(error => console.error('Error fetching data:', error));

				
												};


		function filterDataByShiftAndDay(selectedDate, selectedShift) {
			const date = new Date(selectedDate);

			// Define shift times
			const shifts = {
				'A': { startHour: 6, endHour: 14 },  // 6 AM to 2 PM
				'B': { startHour: 14, endHour: 22 }, // 2 PM to 10 PM
				'C': {
					firstPartStart: 22,  // 10 PM of selected date
					firstPartEnd: 23.99, // 11:59 PM of selected date
					secondPartStart: 0,  // 12 AM (midnight) of next day
					secondPartEnd: 6     // 6 AM of next day
				}
			};

			if (selectedShift === 'C') {
				// First part: 10 PM to 11:59 PM of selected date
				const startTime = new Date(date);
				startTime.setHours(shifts.C.firstPartStart, 0, 0, 0);

				const firstEndTime = new Date(date);
				firstEndTime.setHours(23, 59, 59, 999);

				// Second part: 12 AM to 6 AM of next day
				const nextDayStartTime = new Date(date);
				nextDayStartTime.setDate(nextDayStartTime.getDate() + 1);
				nextDayStartTime.setHours(0, 0, 0, 0);

				const nextDayEndTime = new Date(date);
				nextDayEndTime.setDate(nextDayEndTime.getDate() + 1);
				nextDayEndTime.setHours(6, 0, 0, 0);

				const filteredIndices = allData.timestamps.map((timestamp, index) => {
					const time = new Date(timestamp);

					// Check if the time falls in either the first or second part of the shift
					if ((time >= startTime && time <= firstEndTime) ||
						(time >= nextDayStartTime && time < nextDayEndTime)) {
						return index;
					}
					return null;
				}).filter(index => index !== null);

				return {
					timestamps: getFilteredArray(allData.timestamps, filteredIndices),
					power: getFilteredArray(allData.power, filteredIndices),
					frequency: getFilteredArray(allData.frequency, filteredIndices),
					current: getFilteredArray(allData.current, filteredIndices),
					voltage: getFilteredArray(allData.voltage, filteredIndices),
					speed: getFilteredArray(allData.speed, filteredIndices)
				};
			} else if (selectedShift === 'A') {
				// Shift A: 6 AM to 2 PM of the same day
				const startTime = new Date(date);
				startTime.setHours(shifts.A.startHour, 0, 0, 0);

				const endTime = new Date(date);
				endTime.setHours(shifts.A.endHour, 0, 0, 0);

				const filteredIndices = allData.timestamps.map((timestamp, index) => {
					const time = new Date(timestamp);

					// Check if the time falls within Shift A
					return (time >= startTime && time < endTime) ? index : null;
				}).filter(index => index !== null);

				return {
					timestamps: getFilteredArray(allData.timestamps, filteredIndices),
					power: getFilteredArray(allData.power, filteredIndices),
					frequency: getFilteredArray(allData.frequency, filteredIndices),
					current: getFilteredArray(allData.current, filteredIndices),
					voltage: getFilteredArray(allData.voltage, filteredIndices),
					speed: getFilteredArray(allData.speed, filteredIndices)
				};
			} else if (selectedShift === 'B') {
				// Shift B: 2 PM to 10 PM of the same day
				const startTime = new Date(date);
				startTime.setHours(shifts.B.startHour, 0, 0, 0);

				const endTime = new Date(date);
				endTime.setHours(shifts.B.endHour, 0, 0, 0);

				const filteredIndices = allData.timestamps.map((timestamp, index) => {
					const time = new Date(timestamp);

					// Check if the time falls within Shift B
					return (time >= startTime && time < endTime) ? index : null;
				}).filter(index => index !== null);

				return {
					timestamps: getFilteredArray(allData.timestamps, filteredIndices),
					power: getFilteredArray(allData.power, filteredIndices),
					frequency: getFilteredArray(allData.frequency, filteredIndices),
					current: getFilteredArray(allData.current, filteredIndices),
					voltage: getFilteredArray(allData.voltage, filteredIndices),
					speed: getFilteredArray(allData.speed, filteredIndices)
				};
			} else if (selectedShift === 'all') {
				const startTime = new Date(date);
				startTime.setHours(0, 0, 0, 0);
				const endTime = new Date(date);
				endTime.setHours(23, 59, 59, 999);

				const filteredIndices = allData.timestamps.map((timestamp, index) => {
					const time = new Date(timestamp);
					return (time >= startTime && time <= endTime) ? index : null;
				}).filter(index => index !== null);
			
				return {
					timestamps: getFilteredArray(allData.timestamps, filteredIndices),
					power: getFilteredArray(allData.power, filteredIndices),
					frequency: getFilteredArray(allData.frequency, filteredIndices),
					current: getFilteredArray(allData.current, filteredIndices),
					voltage: getFilteredArray(allData.voltage, filteredIndices),
					speed: getFilteredArray(allData.speed, filteredIndices)
				};
			}
			
		}

		function getFilteredArray(array, indices) {
			const result = [];
			for (let j = 0; j < indices.length; j++) {
				result.push(array[indices[j]]);
			}
			return result;
		}

					   function handleFilterClick() {
			const selectedDate = document.getElementById('selectedDate').value;
			const selectedShift = document.getElementById('shiftSelect').value;
			const selectedMill = document.getElementById('tableSelect').value;

			if (!selectedDate) {
				alert('Please select a date');
				return;
			}

			const filteredData = filterDataByShiftAndDay(selectedDate, selectedShift);

			if (filteredData.timestamps.length === 0) {
				alert('No data available for the selected date and shift');
				return;
			}

			updateCharts(filteredData);



		}


		function initializePage() {
			
			today = new Date();
			document.getElementById('selectedDate').value = today.toISOString().slice(0, 10);
			document.getElementById('shiftSelect').value = 'all';
			
			alert(today);
			filteredData = filterDataByShiftAndDay(today, 'all');
			//this function will update chart
			updateCharts(filteredData);
			// this function will update Detail Grid
		
			getdatabyDateAndShift();
		}

		// Set up event handlers

		//document.getElementById('filterButton').onclick = handleFilterClick;
		//window.onload = initializePage;

		// Set initial date (today)
		window.addEventListener('load', () => {
			
			const today = new Date();
			 document.getElementById('selectedDate').value = today.toISOString().slice(0, 10);
			
			const filteredData = filterDataByDay(today);
			updateCharts(filteredData);
		
				// this function will update Detail Grid
			getdatabyDateAndShift();
			  const DashErrorElements = document.querySelectorAll('.DS-error');
			 DashErrorElements.forEach(el => {
			const value = parseInt(el.getAttribute('data-value'));
			if (value === 1) {
				   el.style.color='red';
							}
			else
				{
				 //  el.style.color='green';
				}
			 });
			
		});
		

		// Format timestamps for the chart
		const timestamps = @Html.Raw(Newtonsoft.Json.JsonConvert.SerializeObject(Model.TubeMills.Select(m => m.Timestamp).ToArray()));
		const powerData = @Html.Raw(Newtonsoft.Json.JsonConvert.SerializeObject(Model.TubeMills.Select(m => m.Actual_kW).ToArray()));
		const frequencyData = @Html.Raw(Newtonsoft.Json.JsonConvert.SerializeObject(Model.TubeMills.Select(m => m.Actual_Frequency).ToArray()));
		const currentData = @Html.Raw(Newtonsoft.Json.JsonConvert.SerializeObject(Model.TubeMills.Select(m => m.Current_Percentage).ToArray()));
		const voltageData = @Html.Raw(Newtonsoft.Json.JsonConvert.SerializeObject(Model.TubeMills.Select(m => m.Voltage_Percentage).ToArray()));
		const speedData = @Html.Raw(Newtonsoft.Json.JsonConvert.SerializeObject(Model.TubeMills.Select(m => m.LineSpeed).ToArray()));

		// Create separate charts
		function createLineChart(ctx, label, data, borderColor) {
			return new Chart(ctx, {
				type: 'line',
				data: {
					labels: timestamps,
					datasets: [{
						label: label,
						data: data,
						borderColor: borderColor,
						borderWidth: 1,
						lineTension: 0.3,
						fill: false
					}]
				},
				options: {
					responsive: true,
					scales: {
						x: {
							type: 'time',
							time: {
								unit: 'minute',
								displayFormats: {
									minute: 'HH:mm:ss'
								}
							},
							title: {
								display: true,
								text: 'Time'
							}
						},
						y: {
							beginAtZero: true,
							title: {
								display: true,
								text: 'Value'
							}
						}
					}
				}
			});
		}

		// Create individual line charts
		const powerChart = createLineChart(
			document.getElementById('powerChart').getContext('2d'),
			'Power',
			powerData,
			'orange'
		);

		const frequencyChart = createLineChart(
			document.getElementById('frequencyChart').getContext('2d'),
			'Frequency',
			frequencyData,
			'blue'
		);

		const speedChart = createLineChart(
			document.getElementById('speedChart').getContext('2d'),
			'Speed',
			speedData,
			'red'
		);

		const voltageCurrentChart = new Chart(document.getElementById('voltageCurrentChart').getContext('2d'), {
			type: 'line',
			data: {
				labels: timestamps,
				datasets: [
					{
						label: 'Voltage',
						data: voltageData,
						borderColor: 'purple',
						borderWidth: 1,
						lineTension: 0.3,
						fill: false
					},
					{
						label: 'Current',
						data: currentData,
						borderColor: 'green',
						borderWidth: 1,
						lineTension: 0.3,
						fill: false
					}
				]
			},
			options: {
				responsive: true,
				scales: {
					x: {
						type: 'time',
						time: {
							unit: 'minute',
							displayFormats: {
								minute: 'HH:mm:ss'
							}
						},
						title: {
							display: true,
							text: 'Time'
						}
					},
					y: {
						beginAtZero: true,
						title: {
							display: true,
							text: 'Value'
						}
					}
				}
			}
		});
		 // here we are changing the chart indicator color with error coloumn value
		 function ErrorIndicator()
			 {
				
			   const DashErrorElementsFirstRow = document.querySelector('.DS');
				  const value1 =DashErrorElementsFirstRow.querySelector('td:nth-child(10)').getAttribute('data-value');

		const errorvalue=value1.split(',').map(item=>item.trim());

				errorvalue.forEach((value, index) => {
		if (value === '') {
			console.log(`Index ${index}: Null or empty value found`);
			// You can add additional handling for empty or null values here
		}
		else {
			console.log(value,index);

			 if(value==='Power')

			{
		document.querySelector('.power').style.backgroundColor = 'red';
			}
				 if(value==='Voltage')
				{
		document.querySelector('.voltage').style.backgroundColor = 'red';
			}
			if(value==='Current')
					{
		document.querySelector('.current').style.backgroundColor = 'red';
			}
			if(value==='Speed')
			{
		document.querySelector('.speed').style.backgroundColor = 'red';
			}
			if(value==='Frequency')
			{
		document.querySelector('.freq').style.backgroundColor = 'red';
			}

			 }
			});


			 //finding the value of column and colouring whole row according to value
			  const DashErrorElementsRow = document.querySelectorAll('.DS');
			 DashErrorElementsRow.forEach(el => {

			   const value =parseInt(el.querySelector('td:nth-child(9)').getAttribute('data-value'));
			if (value === 1) {
				  el.style.color='red';
							}
			else
				{

				}
			 });
			}


		function redirectToPage() {
			window.location.href = "/Home/TubeMillDashboard"

		}
		function redirectToLoginPage() {
			window.location.href = "/Home/Login"
		}

		let timer =setInterval(function()
		{
			location.reload();
		},120000);
		// function showProgress()
		// {
		// 	$("#progressModal").modal("show");
		// }
		// function clossProgress()
		// {
		// 	$("#progressModal").modal("hide");
		// }
	</script>
</body>
</html>
