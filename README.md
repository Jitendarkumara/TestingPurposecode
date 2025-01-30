The issue you're facing is due to the default ISO 8601 format of JavaScript's Date objects when they are serialized as JSON from C#. The format typically looks like this:

2025-01-29T09:02:00.217Z

The "T" is automatically added between the date and time.


---

✅ Solution: Format Timestamp Properly in JavaScript

Modify the JavaScript to convert the Timestamp into a human-readable format before displaying it:

fetch(`/Dashboard/GetFilteredData?selectedDate=${selectedDate}&selectedShift=${selectedShift}&selectedMill=${selectedMill}`)
.then(response => response.json())
.then(data => {
    if (data.length === 0) {
        alert('No data available for the selected criteria');
        return;
    }

    let tableBody = document.getElementById('dataTableBody');
    tableBody.innerHTML = ""; // Clear previous data

    data.forEach(item => {
        // Convert timestamp to a proper format
        let formattedTimestamp = new Date(item.timestamp).toLocaleString('en-US', {
            year: 'numeric', month: '2-digit', day: '2-digit',
            hour: '2-digit', minute: '2-digit', second: '2-digit',
            hour12: false // Use 24-hour format
        });

        let row = `<tr class="DS">
                    <td>${formattedTimestamp}</td>
                    <td>${Math.round(item.voltage * 100) / 100}</td>
                    <td>${Math.round(item.currents * 100) / 100}</td>
                    <td>${Math.round(item.power * 100) / 100}</td>
                    <td>${Math.round(item.frequency * 100) / 100}</td>
                    <td>${Math.round(item.speed * 100) / 100}</td>
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


---

🔹 How This Fix Works

1. ✅ Uses new Date(item.timestamp) to parse the string into a JavaScript Date object.


2. ✅ Uses .toLocaleString('en-US', {...}) to format it properly:

Removes the "T"

Keeps YYYY-MM-DD HH:mm:ss format

Uses 24-hour format



3. ✅ Fixes rounding issues for numerical values (Math.round(value * 100) / 100) to ensure two decimal places.




---

🚀 Now, The Timestamp Will Display Correctly Without "T"!

Let me know if you need further refinements!

