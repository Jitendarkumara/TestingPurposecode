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
		let row = `<tr class="DS">
					<td>${(item.timestamp)}</td>
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
