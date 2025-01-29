document.getElementById('filterButton').addEventListener('click', () => {
    const selectedDate = document.getElementById('selectedDate').value;
    const selectedShift = document.getElementById('shiftSelect').value;
    const selectedMill = document.getElementById('tableSelect').value;

    if (!selectedDate || !selectedShift || !selectedMill) {
        alert('Please select a date, shift, and mill');
        return;
    }

    // Convert selectedDate to JavaScript Date object
    const selectedDateObj = new Date(selectedDate);

    // Define shift timing
    let shiftStart, shiftEnd;

    if (selectedShift === "Morning") {
        shiftStart = new Date(selectedDateObj.setHours(6, 0, 0, 0));  // 06:00 AM
        shiftEnd = new Date(selectedDateObj.setHours(14, 0, 0, 0));   // 02:00 PM
    } else if (selectedShift === "Evening") {
        shiftStart = new Date(selectedDateObj.setHours(14, 0, 0, 0));  // 02:00 PM
        shiftEnd = new Date(selectedDateObj.setHours(22, 0, 0, 0));    // 10:00 PM
    } else if (selectedShift === "Night") {
        shiftStart = new Date(selectedDateObj.setHours(22, 0, 0, 0));  // 10:00 PM
        shiftEnd = new Date(selectedDateObj.setHours(6, 0, 0, 0));     // 06:00 AM next day
        shiftEnd.setDate(shiftEnd.getDate() + 1); // Move end time to the next day
    }

    // Get data from Model.Alarm (already available in Razor)
    let filteredData = alarmData.filter(item => {
        let itemDate = new Date(item.Timestamp);
        return item.Mill === selectedMill &&
               itemDate >= shiftStart &&
               itemDate < shiftEnd;
    });

    if (filteredData.length === 0) {
        alert('No data available for the selected criteria');
        return;
    }

    // Update table content
    let tableBody = document.getElementById('dataTableBody');
    tableBody.innerHTML = ""; // Clear previous data

    filteredData.forEach(item => {
        let row = `<tr class="DS">
                    <td>${item.Timestamp}</td>
                    <td>${Math.round(item.Voltage, 2)}</td>
                    <td>${Math.round(item.Currents, 2)}</td>
                    <td>${Math.round(item.Power, 2)}</td>
                    <td>${Math.round(item.Frequency, 2)}</td>
                    <td>${Math.round(item.Speed, 2)}</td>
                    <td>${item.OD}</td>
                    <td>${item.Grade}</td>
                    <td class="DS-error" data-value="${item.alarm}">${item.Mill}</td>
                    <td data-value="${item.Erroneous_Parameter}">${item.Erroneous_Parameter}</td>
                </tr>`;
        tableBody.innerHTML += row;
    });
});