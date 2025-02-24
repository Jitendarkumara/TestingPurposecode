

<!DOCTYPE html>
<html lang="en">
<head>
    <title>KWH Chart</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/moment@2.29.1"></script>
    <script src="https://cdn.jsdelivr.net/npm/chartjs-adapter-moment"></script>

    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
        }

        #chart-container {
            width: 800px;
            height: 500px;
            margin: auto;
            padding: 20px;
            background-color: #f4f4f4;
            border-radius: 10px;
            box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);
        }

        select {
            margin: 20px;
            padding: 10px;
            font-size: 16px;
        }
    </style>
</head>
<body>
    <h2>KWH Consumption Chart</h2>

    <label for="millSelect">Select Mill:</label>
    <select id="millSelect">
         
        
    </select>

    <div id="chart-container">
        <canvas id="kwhChart"></canvas>
    </div>

    <script>
        let kwhChart; // Store the chart instance

               async function loadChartData(selectedMill) {
            try {
                let url = "/Home/GetKWHData"; // Base API endpoint

                // Append the selected mill as a query parameter
                if (selectedMill) {
                    url += `?millName=${encodeURIComponent(selectedMill)}`;
                }

                const response = await fetch(url);

                if (!response.ok) {
                    throw new Error(`HTTP error! Status: ${response.status}`);
                }

                const data = await response.json();

                console.log("Fetched Data:", data);

                if (!data || data.length === 0) {
                    alert("No data available!");
                    return;
                }

                // Filter data based on selected mill
                let filteredData = selectedMill ? data.filter(item => item.millname.trim() === selectedMill) : data;

                if (filteredData.length === 0) {
                    alert("No data found for the selected mill!");
                    return;
                }

                // Extract timestamps and KWH values
                const timestamps = filteredData.map(item => new Date(item.timeStamp));
                const kwhData = filteredData.map(item => parseFloat(item.kwh));

                console.log("Timestamps:", timestamps);
                console.log("KWH Data:", kwhData);

                // Define LSL & USL values
                const lslData = new Array(timestamps.length).fill(10); // Example LSL
                const uslData = new Array(timestamps.length).fill(50); // Example USL

                let ctx = document.getElementById("kwhChart").getContext("2d");

                // If chart already exists, update it
                if (kwhChart) {
                    kwhChart.data.labels = timestamps;
                    kwhChart.data.datasets[0].data = kwhData;
                    kwhChart.update();
                } else {
                    // Create a new chart
                    kwhChart = new Chart(ctx, {
                        type: "line",
                        data: {
                            labels: timestamps,
                            datasets: [
                                {
                                    label: "KWH",
                                    data: kwhData,
                                    borderColor: "blue",
                                    backgroundColor: "rgba(0, 123, 255, 0.3)",
                                    fill: true
                                },
                                {
                                    label: "LSL",
                                    data: lslData,
                                    borderColor: "orange",
                                    borderDash: [5, 5],
                                    fill: false
                                },
                                {
                                    label: "USL",
                                    data: uslData,
                                    borderColor: "gray",
                                    borderDash: [5, 5],
                                    fill: false
                                }
                            ]
                        },
                        options: {
                            responsive: true,
                            maintainAspectRatio: false,
                            plugins: {
                                tooltip: {
                                    callbacks: {
                                        label: function (tooltipItem) {
                                            let datasetLabel = tooltipItem.dataset.label || "";
                                            let value = tooltipItem.raw;
                                            let timestamp = new Date(timestamps[tooltipItem.dataIndex]).toLocaleString();
                                            return `${timestamp} - ${datasetLabel}: ${value}`;
                                        }
                                    }
                                }
                            },
                            scales: {
                                x: {
                                    type: "time",
                                    time: {
                                        unit: "day",
                                        displayFormats: { day: "MMM d, yyyy HH:mm" }
                                    },
                                    grid: { color: "#eee" },
                                    title: { display: true, text: "Timestamp" }
                                },
                                y: {
                                    beginAtZero: true,
                                    grid: { color: "#ddd" },
                                    title: { display: true, text: "KWH Value" }
                                }
                            }
                        }
                    });
                }
            } catch (error) {
                console.error("Error loading chart data:", error);
                alert(`Failed to load data. ${error.message}`);
            }
        }
        async function loadMillname() {
            try {
                let url = "/Home/GetAllMill"; // API endpoint
                const response = await fetch(url);
                const data = await response.json();

                console.log("Fetched Data:", data); // Debugging

                if (!data || data.length === 0) {
                    alert("No Mill Name available!");
                    return;
                }

                // Get the dropdown element
                let millDropdown = document.getElementById("millSelect");
                millDropdown.innerHTML = '<option value="">All Mills</option>'; // Default option

                // Add fetched mills to the dropdown
                data.forEach(mill => {
                    let option = document.createElement("option");
                    option.value = mill.allMillName; // Correct property name
                    option.textContent = mill.allMillName; // Display name
                    millDropdown.appendChild(option);
                });

            } catch (error) {
                console.error("Error loading Mill Name:", error);
                alert("Failed to load Mill Name.");
            }
        }
        
        // Load chart on page load
        loadMillname();


     // let  MillName= document.getElementById("millSelect").value;
     //       loadChartData(MillName);

        document.getElementById("millSelect").addEventListener("change", function () {
            let selectedMill = this.value || null;
            loadChartData(selectedMill);
        });
    </script>
</body>
</html>
