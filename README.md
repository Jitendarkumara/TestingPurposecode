	data.forEach(item => {
				let dateObj = new Date(item.timestamp);
	let formattedTimestamp = new Intl.DateTimeFormat('en-GB', {
	year: 'numeric', month: '2-digit', day: '2-digit',
	hour: '2-digit', minute: '2-digit', second: '2-digit',
	hourCycle: 'h23' // Ensures 24-hour format
}).format(dateObj).replace(',', '');

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
