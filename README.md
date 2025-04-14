<!-- Filter button event binding and fetchFilteredData function -->
<script>
    // Global chart variables
    let powerChart, frequencyChart, speedChart, voltageCurrentChart;

    // Function to update charts using the fetched data
    function updateCharts(data) {
        console.log("updateCharts() called with data:", data);
        const timestamps = data.map(item => item.timestamp);
        const powerData = data.map(item => item.actual_kW);
        const frequencyData = data.map(item => item.actual_Frequency);
        const currentData = data.map(item => item.current_Percentage);
        const voltageData = data.map(item => item.voltage_Percentage);
        const speedData = data.map(item => item.lineSpeed);

        if (!powerChart) {
            // Create new charts if not already created
            powerChart = new Chart(document.getElementById('powerChart').getContext('2d'), {
                type: 'line',
                data: {
                    labels: timestamps,
                    datasets: [{
                        label: 'Power',
                        data: powerData,
                        borderColor: 'orange',
                        borderWidth: 1,
                        fill: false
                    }]
                },
                options: {
                    responsive: true,
                    scales: {
                        x: { 
                            type: 'time',
                            time: { unit: 'minute', displayFormats: { minute: 'HH:mm:ss' } },
                            title: { display: true, text: 'Time' }
                        },
                        y: { beginAtZero: true, title: { display: true, text: 'Power (kW)' } }
                    }
                }
            });
            // Create the other charts similarly...
            frequencyChart = new Chart(document.getElementById('frequencyChart').getContext('2d'), {
                type: 'line',
                data: {
                    labels: timestamps,
                    datasets: [{
                        label: 'Frequency',
                        data: frequencyData,
                        borderColor: 'blue',
                        borderWidth: 1,
                        fill: false
                    }]
                },
                options: {
                    responsive: true,
                    scales: {
                        x: { 
                            type: 'time',
                            time: { unit: 'minute', displayFormats: { minute: 'HH:mm:ss' } },
                            title: { display: true, text: 'Time' }
                        },
                        y: { beginAtZero: true, title: { display: true, text: 'Frequency (kHz)' } }
                    }
                }
            });
            speedChart = new Chart(document.getElementById('speedChart').getContext('2d'), {
                type: 'line',
                data: {
                    labels: timestamps,
                    datasets: [{
                        label: 'Speed',
                        data: speedData,
                        borderColor: 'red',
                        borderWidth: 1,
                        fill: false
                    }]
                },
                options: {
                    responsive: true,
                    scales: {
                        x: { 
                            type: 'time',
                            time: { unit: 'minute', displayFormats: { minute: 'HH:mm:ss' } },
                            title: { display: true, text: 'Time' }
                        },
                        y: { beginAtZero: true, title: { display: true, text: 'Speed (mpm)' } }
                    }
                }
            });
            voltageCurrentChart = new Chart(document.getElementById('voltageCurrentChart').getContext('2d'), {
                type: 'line',
                data: {
                    labels: timestamps,
                    datasets: [
                        {
                            label: 'Voltage',
                            data: voltageData,
                            borderColor: 'purple',
                            borderWidth: 1,
                            fill: false
                        },
                        {
                            label: 'Current',
                            data: currentData,
                            borderColor: 'green',
                            borderWidth: 1,
                            fill: false
                        }
                    ]
                },
                options: {
                    responsive: true,
                    scales: {
                        x: { 
                            type: 'time',
                            time: { unit: 'minute', displayFormats: { minute: 'HH:mm:ss' } },
                            title: { display: true, text: 'Time' }
                        },
                        y: { beginAtZero: true, title: { display: true, text: 'Value' } }
                    }
                }
            });
        } else {
            // Update existing charts
            powerChart.data.labels = timestamps;
            powerChart.data.datasets[0].data = powerData;
            powerChart.update();

            frequencyChart.data.labels = timestamps;
            frequencyChart.data.datasets[0].data = frequencyData;
            frequencyChart.update();

            speedChart.data.labels = timestamps;
            speedChart.data.datasets[0].data = speedData;
            speedChart.update();

            voltageCurrentChart.data.labels = timestamps;
            voltageCurrentChart.data.datasets[0].data = voltageData;
            voltageCurrentChart.data.datasets[1].data = currentData;
            voltageCurrentChart.update();
        }
    }

    // Function to update the detail table with the fetched data
    function updateDetailTable(data) {
        const tableBody = document.getElementById('dataTableBody');
        tableBody.innerHTML = "";
        data.forEach(item => {
            const d = new Date(item.timestamp);
            const formattedTimestamp = 
                String(d.getDate()).padStart(2, '0') + "-" +
                String(d.getMonth() + 1).padStart(2, '0') + "-" +
                d.getFullYear() + " " +
                String(d.getHours()).padStart(2, '0') + ":" +
                String(d.getMinutes()).padStart(2, '0') + ":" +
                String(d.getSeconds()).padStart(2, '0');

            const row = `<tr class="DS">
                            <td>${formattedTimestamp}</td>
                            <td>${Math.round(item.voltage_Percentage || 0)}</td>
                            <td>${Math.round(item.current_Percentage || 0)}</td>
                            <td>${Math.round(item.actual_kW || 0)}</td>
                            <td>${Math.round(item.actual_Frequency || 0)}</td>
                            <td>${Math.round(item.lineSpeed || 0)}</td>
                            <td>${item.od || ''}</td>
                            <td>${item.grade || ''}</td>
                            <td class="DS-error" data-value="${item.alarm || ''}">${item.mill || ''}</td>
                            <td data-value="${item.erroneous_Parameter || ''}">${item.erroneous_Parameter || ''}</td>
                        </tr>`;
            tableBody.innerHTML += row;
        });
        updateErrorIndicators();
    }

    // Function to update error indicators based on table values
    function updateErrorIndicators() {
        const errorElements = document.querySelectorAll('.DS-error');
        errorElements.forEach(el => {
            const val = el.getAttribute('data-value') || "";
            if (val === "1" || val.toLowerCase() === "power") {
                el.style.color = 'red';
            } else {
                el.style.color = 'green';
            }
        });
    }

    // Main function: fetch filtered data and update charts and table
    function fetchFilteredData() {
        const selectedDate = document.getElementById('selectedDate').value;
        const selectedShift = document.getElementById('shiftSelect').value;
        const selectedMill = document.getElementById('tableSelect').value;

        if (!selectedDate || !selectedShift || !selectedMill) {
            alert('Please select a date, shift, and mill');
            return;
        }

        console.log("Fetching data for:", selectedDate, selectedShift, selectedMill);
        fetch(`/Dashboard/GetFilteredData?selectedDate=${selectedDate}&selectedShift=${selectedShift}&selectedMill=${selectedMill}`)
            .then(response => response.json())
            .then(data => {
                console.log("Data received:", data);
                if (!data || data.length === 0) {
                    alert('No data available for the selected criteria');
                    return;
                }
                updateCharts(data);
                updateDetailTable(data);
            })
            .catch(error => console.error('Error fetching data:', error));
    }

    // Redirection helper functions
    function redirectToPage() {
        window.location.href = "/Home/TubeMillDashboard";
    }
    function redirectToLoginPage() {
        window.location.href = "/Home/Login";
    }

    // Initialization: set default date, shift and fetch initial data
    function initializePage() {
        const today = new Date().toISOString().slice(0, 10);
        document.getElementById('selectedDate').value = today;
        document.getElementById('shiftSelect').value = 'all';
        fetchFilteredData();
    }

    // Bind event handler for filter button
    document.getElementById('filterButton').addEventListener('click', (e) => {
        e.preventDefault();
        console.log("Filter button clicked");
        fetchFilteredData();
    });

    window.addEventListener('load', initializePage);

    // Optional: Auto-refresh page every 2 minutes
    setInterval(() => {
        location.reload();
    }, 120000);
</script>
</body>
</html>