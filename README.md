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
    <style>
        /* Example CSS – adjust or move to your Dashboard.css as needed */
        .charts-container { margin-top: 20px; }
        .chart-container { margin-bottom: 30px; }
        .date-filter { margin-bottom: 20px; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        table, th, td { border: 1px solid #ddd; }
        th, td { padding: 8px; text-align: center; }
        .ErrorIndicator { display: inline-block; }
    </style>
</head>
<body>
    <div class="container">
        <!-- Table Selection and Filters -->
        <div class="date-filter">
            <button type="button" id="logoutBtn" class="btn btn-warning shadow-sm top-right-button fw-bold" onclick="redirectToLoginPage()">Log Out</button>
            <button type="button" class="btn btn-primary shadow-sm top-right-button-2 fw-bold" onclick="redirectToPage()">Go To TubeMill Dashboard Page</button>

            <div>
                <label for="tableSelect">Select TubeMill :</label>
                <select id="tableSelect" name="tableName">
                    <option value="TubeMill1">TubeMill1</option>
                    <option value="TubeMill2">TubeMill2</option>
                    <option value="TubeMill3" selected>TubeMill3</option>
                    <option value="TubeMill4">TubeMill4</option>
                    <option value="TubeMill7">TubeMill7</option>
                </select>
            </div>

            <div class="filter-group" style="margin-top: 10px;">
                <label for="selectedDate">Select Date:</label>
                <input type="date" id="selectedDate" name="selectedDate" />
                <label for="shiftSelect">Select Shift:</label>
                <select id="shiftSelect" name="shiftSelect">
                    <option value="all">All_Shifts</option>
                    <option value="A">A_Shift</option>
                    <option value="B">B_Shift</option>
                    <option value="C">C_Shift</option>
                </select>
                <button id="filterButton" class="btn btn-success">Show Data</button>
            </div>
        </div>

        <!-- Status Indicators -->
        <div style="display: flex; align-items: center; gap: 10px;">
            <div class="ErrorIndicator" style="width: 20px; height: 20px; background-color: red; border-radius: 50%;"></div>
            <span>Red circle indicates parameters cause for defect</span>
            <div class="ErrorIndicator" style="width: 20px; height: 20px; background-color: green; border-radius: 50%;"></div>
            <span>Green circle indicates no defect</span>
        </div>

        <!-- Loading Modal (optional) -->
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
            <!-- First Row -->
            <div class="charts-row row">
                <div class="col-md-6 chart-container">
                    <div style="display:flex; align-items:center; gap:10px;">
                        <h3>Power</h3>
                        <div class="ErrorIndicator power" data-value=""></div>
                    </div>
                    <canvas id="powerChart"></canvas>
                </div>
                <div class="col-md-6 chart-container">
                    <div style="display:flex; align-items:center; gap:10px;">
                        <h3>Frequency</h3>
                        <div class="ErrorIndicator freq" data-value=""></div>
                    </div>
                    <canvas id="frequencyChart"></canvas>
                </div>
            </div>
            <!-- Second Row -->
            <div class="charts-row row">
                <div class="col-md-6 chart-container">
                    <div style="display:flex; align-items:center; gap:10px;">
                        <h3>Speed</h3>
                        <div class="ErrorIndicator speed" data-value=""></div>
                    </div>
                    <canvas id="speedChart"></canvas>
                </div>
                <div class="col-md-6 chart-container">
                    <div style="display:flex; align-items:center; gap:10px;">
                        <h3>Voltage and Current</h3>
                        <div class="ErrorIndicator voltage" data-value=""></div>
                        <div class="ErrorIndicator current" data-value=""></div>
                    </div>
                    <canvas id="voltageCurrentChart"></canvas>
                </div>
            </div>
        </div>

        <!-- Input Panel -->
        <form asp-action="SaveTubeInput" method="post" style="margin-top:30px;">
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
            <div class="mt-3 text-center">
                <button type="submit" id="submitButton" class="btn btn-primary">Submit</button>
            </div>
        </form>

        <!-- Detail View Table -->
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
                    <th>Grade</th>
                    <th>MillNo</th>
                    <th>Error</th>
                </tr>
            </thead>
            <tbody id="dataTableBody">
                <!-- Data rows will be appended here dynamically -->
            </tbody>
        </table>
    </div>

    <!-- Scripts -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/moment"></script>
    <script src="https://cdn.jsdelivr.net/npm/chartjs-adapter-moment"></script>
    <script>
        // Global chart variables
        let powerChart, frequencyChart, speedChart, voltageCurrentChart;

        // Update charts using the fetched data
        function updateCharts(data) {
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
                            y: { beginAtZero: true, title: { display: true, text: 'Value' } }
                        }
                    }
                });
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
                            y: { beginAtZero: true, title: { display: true, text: 'Value' } }
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
                            y: { beginAtZero: true, title: { display: true, text: 'Value' } }
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
                // Otherwise, update existing charts
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

        // Update detail table with fetched data
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

        // Optionally update error indicator colors based on table values
        function updateErrorIndicators() {
            const errorElements = document.querySelectorAll('.DS-error');
            errorElements.forEach(el => {
                const val = el.getAttribute('data-value') || "";
                // Example: if error value is '1' or a certain word, mark red.
                if (val === "1" || val.toLowerCase() === "power") {
                    el.style.color = 'red';
                } else {
                    el.style.color = 'green';
                }
            });
        }

        // Main function: fetch filtered data from server and update charts/table
        function fetchFilteredData() {
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
            // Fetch initial data from server based on today's date, 'all' shift, and default mill.
            fetchFilteredData();
        }

        // Bind event handlers
        document.getElementById('filterButton').addEventListener('click', (e) => {
            e.preventDefault();
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