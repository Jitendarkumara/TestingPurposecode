@foreach (var item in filteredData)
{

				<tr class="DS">
					<td>@item.Timestamp</td>
					<td>@Math.Round(@item.Voltage, 2)</td>
					<td>@Math.Round(@item.Currents, 2)</td>
					<td>@Math.Round(@item.Power, 2)</td>
					<td>@Math.Round(@item.Frequency, 2)</td>
					<td>@Math.Round(@item.Speed, 2)</td>
					<td>@item.OD</td>

					<td>@item.Grade</td>

					<td class="DS-error" data-value="@item.alarm.ToString()">@item.Mill</td>
					@* <td class="DS-error" data-value="@item.alarm.ToString()">@item.alarm</td> *@
					<td data-value="@item.Erroneous_Parameter">@item.Erroneous_Parameter</td>

				</tr>

}
		var filteredData = Model.Alarm.Where(item => item.Mill == selectedMill && item.timestamp==selectedDate).ToList();

					
			
document.getElementById('filterButton').addEventListener('click', () => {
	const selectedDate = document.getElementById('selectedDate').value;
 const selectedShift = document.getElementById('shiftSelect').value;
			const selectedMill = document.getElementById('tableSelect').value;
				const selectedMill = document.getElementById('shiftSelect').value;
	if (!selectedDate) {
		alert('Please select a date');
		return;
	}
	//---here is coding to recieve data after filtering date and mill 
	var filteredData = Model.Alarm.Where(item => item.Mill == selectedMill && item.timestamp==selectedDate).ToList();

	
	//const filteredData = filterDataByDay(selectedDate);

	if (filteredData.timestamps.length === 0) {
		alert('No data available for the selected date');
		return;
	}
	
});
			
