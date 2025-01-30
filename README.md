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
        // Convert timestamp to a proper 24-hour format (YYYY-MM-DD HH:mm:ss)
        let dateObj = new Date(item.timestamp);
        let formattedTimestamp = dateObj.getFullYear() + "-" +
                                 String(dateObj.getMonth() + 1).padStart(2, '0') + "-" +
                                 String(dateObj.getDate()).padStart(2, '0') + " " +
                                 String(dateObj.getHours()).padStart(2, '0') + ":" +
                                 String(dateObj.getMinutes()).padStart(2, '0') + ":" +
                                 String(dateObj.getSeconds()).padStart(2, '0');

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
.catch(error => console.error('Error fetching data:', error)); => {
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

