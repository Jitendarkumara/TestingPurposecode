public IActionResult GetFilteredData(DateTime selectedDate, string selectedShift, string selectedMill)
{
    using (var context = new YourDbContext())
    {
        // Define shift timing
        TimeSpan shiftStart, shiftEnd;

        if (selectedShift == "Morning")
        {
            shiftStart = new TimeSpan(6, 0, 0);  // 06:00 AM
            shiftEnd = new TimeSpan(14, 0, 0);   // 02:00 PM
        }
        else if (selectedShift == "Evening")
        {
            shiftStart = new TimeSpan(14, 0, 0);  // 02:00 PM
            shiftEnd = new TimeSpan(22, 0, 0);    // 10:00 PM
        }
        else if (selectedShift == "Night")
        {
            shiftStart = new TimeSpan(22, 0, 0);  // 10:00 PM
            shiftEnd = new TimeSpan(6, 0, 0);     // 06:00 AM (next day)
        }
        else
        {
            return Json(new { success = false, message = "Invalid shift selected." });
        }

        // Convert selectedDate to the start and end datetime
        DateTime shiftStartDateTime = selectedDate.Date + shiftStart;
        DateTime shiftEndDateTime = selectedDate.Date + shiftEnd;

        // Special case for Night shift (crosses midnight)
        if (selectedShift == "Night")
        {
            shiftEndDateTime = shiftEndDateTime.AddDays(1);
        }

        // Query data from Entity Framework
        var filteredData = context.Alarm
            .Where(item => item.Mill == selectedMill &&
                           item.Timestamp >= shiftStartDateTime &&
                           item.Timestamp < shiftEndDateTime)
            .ToList();

        return Json(filteredData);
    }
}
_-----------

document.getElementById('filterButton').addEventListener('click', () => {
    const selectedDate = document.getElementById('selectedDate').value;
    const selectedShift = document.getElementById('shiftSelect').value;
    const selectedMill = document.getElementById('tableSelect').value;

    if (!selectedDate || !selectedShift || !selectedMill) {
        alert('Please select a date, shift, and mill');
        return;
    }

    // Send AJAX request to the server
    fetch(`/YourController/GetFilteredData?selectedDate=${selectedDate}&selectedShift=${selectedShift}&selectedMill=${selectedMill}`)
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
        })
        .catch(error => console.error('Error fetching data:', error));
});