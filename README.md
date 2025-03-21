<!DOCTYPE html>
<html lang="en">
<head>
    <title>KWH Chart</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/moment@2.29.1"></script>
    <script src="https://cdn.jsdelivr.net/npm/chartjs-adapter-moment"></script>
    <script src="https://cdn.jsdelivr.net/npm/chartjs-plugin-zoom"></script>

    <style>
        

        #chart-container {
            width: 100%;
            height: 600px;
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
        /* Saurabh */
        .filter-container {
            background: #ffffff;
            padding: 20px;
            border-radius: 12px;
            box-shadow: 0px 5px 20px rgba(0, 0, 0, 0.15);
        }

        label {
            font-weight: 600;
            margin-bottom: 5px;
        }

        .form-select, .form-control {
            border-radius: 8px;
            height: 42px;
        }

        .btn-custom {
            background: linear-gradient(135deg, #007bff, #0056b3);
            color: white;
            font-weight: bold;
            border-radius: 8px;
            padding: 10px 20px;
            transition: 0.3s;
            width: 100%;
        }

            .btn-custom:hover {
                background: linear-gradient(135deg, #0056b3, #00408a);
            }

    </style>
</head>
<body>
 
    <div class="card shadow-lg p-3 rounded-4" style="background: linear-gradient(90deg, #e3f2fd, #4caf50); border: none; margin-top:5px ;margin-bottom:10px">
        <div class="card-header text-white text-center py-2 rounded-3" style="background: linear-gradient(135deg, #1976d2, #2196f3); font-size: 1.2rem; font-weight: 600;">
            Filter Options
        </div>
        <div class="card-body">
            <div class="row g-3">
                <div class="col-md-2">
                    <label for="millSelect" class="fw-bold">Select Mill</label>
                    <select id="millSelect" class="form-select m-0"></select>
                </div>
                <div class="col-md-2">
                    <label for="FeederSelect" class="fw-bold">Select Feeder</label>
                    <select id="FeederSelect" class="form-select m-0"></select>
                </div>
                <div class="col-md-2">
                    <label for="dateFilter" class="fw-bold">Select Date</label>
                    <input type="date" id="dateFilter" class="form-control m-0">
                </div>
                <div class="col-md-2">
                    <label for="shiftSelect" class="fw-bold">Select Shift</label>
                    <select id="shiftSelect" class="form-select m-0">
                        <option selected disabled>Select Shift</option>
                        <option value="All">All</option>
                        <option value="A">Shift A</option>
                        <option value="B">Shift B</option>
                        <option value="C">Shift C</option>
                    </select>
                </div>
                <div class="col-md-2">
                    <label for="statusSelect" class="fw-bold">Select Status</label>
                    <select id="statusSelect" class="form-select m-0">
                        <option selected disabled>Select Status</option>
                        <option value="All">All</option>
                        <option value="Running">Running</option>
                        <option value="Stop">Stop</option>
                    </select>
                </div>
            </div>
        </div>
    </div>

    <div id="chart-container" style="margin-top:3px">
        <canvas id="kwhChart"></canvas>
    </div>

    <script>
        let kwhChart; // Store the chart instance

               async function loadChartData(selectedMill="Mill1",selectedFeeder="All",seletedDate) {
            try {
                alert(selectedDate);

                                let url = `/Home/GetKWHData?millName=${encodeURIComponent(selectedMill)}&feederName=${encodeURIComponent(selectedFeeder)}&feederName=${encodeURIComponent(seletedDate)}`;

                const response = await fetch(url);

                if (!response.ok) {
                    throw new Error(`HTTP error! Status: ${response.status}`);
                }

                const data = await response.json();

                console.log("Fetched Data:", data);

                if (!data || data.length === 0) {
                 //   alert("No data available!");
                    return;
                }

                // Filter data based on selected mill
                let filteredData = selectedMill ? data.filter(item => item.millname.trim() === selectedMill) : data;

                if (filteredData.length === 0) {
                  //  alert("No data found for the selected mill!");
                    return;
                }

                // Extract timestamps and KWH values
                const timestamps = filteredData.map(item => new Date(item.timeStamp));
                const kwhData = filteredData.map(item => parseFloat(item.kwh));

                console.log("Timestamps:", timestamps);
                console.log("KWH Data:", kwhData);

                // Define LSL & USL values
                              var lslData = new Array(timestamps.length).fill(10); // Example LSL
                        var uslData = new Array(timestamps.length).fill(50); // Example USL
                                // const lslData = 25; // Example LSL
                                // const uslData = 55; // Example USL
                        if(selectedMill==='Mill1')
                           {   
                      
                       lslData = new Array(timestamps.length).fill(10); // Example LSL
                                uslData = new Array(timestamps.length).fill(43);
                           }
                        else if(selectedMill==='Finishing-2')
                                   {

                               lslData = new Array(timestamps.length).fill(5); // Example LSL
                                        uslData = new Array(timestamps.length).fill(40);
                                   }
                       else  if(selectedMill==='Ext-coating')
                                           {

                                       lslData = new Array(timestamps.length).fill(31); // Example LSL
                                                uslData = new Array(timestamps.length).fill(180);
                                           }

                          else if(selectedMill==='Finishing-1')
                                           {

                                               lslData = new Array(timestamps.length).fill(5); // Example LSL
                                                        uslData = new Array(timestamps.length).fill(25);
                                           }

                                   
                    else{  
                                     lslData = new Array(timestamps.length).fill(8); // Example LSL
                                        uslData = new Array(timestamps.length).fill(50);
                      }
          

                let ctx = document.getElementById("kwhChart").getContext("2d");

                // If chart already exists, update it
                if (kwhChart) {
                    kwhChart.data.labels = timestamps;
                    kwhChart.data.datasets[0].data = kwhData;
                                kwhChart.data.datasets[1].data = lslData;
                                            kwhChart.data.datasets[2].data = uslData;
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
                                 //   label: "LSL",

                                             label: "Lower Limit",
                                    data: lslData,
                                    borderColor: "green",
                                    borderDash: [5, 5],
                                    fill: false
                                },
                                {
                                    label: "Upper Limit",
                                    data: uslData,
                                    borderColor: "red",
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
                                            // return `${timestamp} - ${datasetLabel}: ${value}`;
                                            return `${datasetLabel}: ${value}`;
                                        }
                                    }
                                        }, zoom: {
                    pan: {
                        enabled: true,
                        mode: "x", // Allow horizontal scrolling
                        speed: 10
                    },
                    zoom: {
                        wheel: {
                            enabled: true,
                            mode: "x" // Zoom only on x-axis
                        },
                        pinch: {
                            enabled: true
                        },
                        mode: "x"
                    }
                }


                            },
                            scales: {
x: {
            type: "time",
            time: {
                unit: "minute",
                displayFormats: { minute: "hh:mm a" }
            },
            grid: { color: "#eee" },
            title: { display: true, text: "Timestamp" },
// //-------------------only time------------------------
//             // ticks: {
//             //     source: 'data',  // <-- Use only actual data points
//             //     autoSkip: false,
//             //     callback: function(value, index, values) {
//             //         let actualDataTimes = timestamps.map(t => new Date(t).getTime()); // Convert to milliseconds
//             //         if (actualDataTimes.includes(value)) {
//             //             return new Date(value).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', hour12: true });
//             //         } else {
//             //             return ""; // Hide non-data ticks
//             //         }
//             //     }
//             // }
//  //-------------date with time ----------------------
//         //         ticks: {
//         //     source: 'data',  // Use only actual data points
//         //     autoSkip: false,  // Prevent automatic skipping
//         //     maxRotation: 0,   // Prevents angled text
//         //     minRotation: 0,   // Forces horizontal text
//         //     callback: function(value, index, values) {
//         //         let actualDataTimes = timestamps.map(t => new Date(t).getTime()); // Convert to milliseconds
//         //         if (actualDataTimes.includes(value)) {
//         //             return new Date(value).toLocaleString([], {
//         //                 year: 'numeric',
//         //                 month: 'short',
//         //                 day: '2-digit',
//         //                 hour: '2-digit',
//         //                 minute: '2-digit',
//         //                 second: '2-digit',
//         //                 hour12: true
//         //             });
//         //         } else {
//         //             return null; // Hides non-data points
//         //         }
//         //     }
//         // }

                           
// //---------------Testing code--------------------

        ticks: {
            source: 'data',  // Use only actual data points
            autoSkip: true,  // Allow automatic skipping to prevent overlap
            autoSkipPadding: 20, // Adds padding to prevent excessive skips
            maxRotation: 45,  // Rotate labels to avoid overlapping
            minRotation: 30,  // Ensures labels are always rotated slightly
            callback: function(value, index, values) {
                let actualDataTimes = timestamps.map(t => new Date(t).getTime()); // Convert to milliseconds
                if (actualDataTimes.includes(value)) {
                    return new Date(value).toLocaleString([], {
                        year: 'numeric',
                        month: 'short',
                        day: '2-digit',
                        hour: '2-digit',
                        minute: '2-digit',
                        second: '2-digit',
                        hour12: true
                    });
                } else {
                    return ""; // Hide non-data points properly
                }
            }
        }

//-----------------------------------
        }

              
                            }
                        }


                    });
                }
            } catch (error) {
                console.error("Error loading chart data:", error);
              //  alert(`Failed to load data. ${error.message}`);
            }
        }
        async function loadMillname() {
            try {
                let url = "/Home/GetAllMill"; // API endpoint
                const response = await fetch(url);
                const data = await response.json();

                console.log("Fetched Data:", data); // Debugging

                if (!data || data.length === 0) {
                  //  alert("No Mill Name available!");
                    return;
                }

                // Get the dropdown element
                let millDropdown = document.getElementById("millSelect");
            /*     millDropdown.innerHTML = '<option value="Mill1">Mill1</option>'; */ // Default option

                // Add fetched mills to the dropdown
                data.forEach(mill => {
                    let option = document.createElement("option");
                    option.value = mill.allMillName; // Correct property name
                    option.textContent = mill.allMillName; // Display name
                    millDropdown.appendChild(option);
                });
                        let selectedMill=data[0].allMillName;
                 loadFeedername(selectedMill);

            } catch (error) {
              //  console.error("Error loading Mill Name:", error);
              //  alert("Failed to load Mill Name.");
            }
        }
        async function loadFeedername(selectedMill) {
            try {
                let url = "/Home/GetFeeder"; // API endpoint
                if (selectedMill) {
                    url += `?millName=${encodeURIComponent(selectedMill)}`;
                }

                const response = await fetch(url);
                const data = await response.json();

                console.log("Fetched Feeder Data:", data); // Debugging

                if (!data || data.length === 0) {
                    console.warn("No Feeder available!");
                    return;
                }

                // Get the dropdown element
                let feederDropdown = document.getElementById("FeederSelect");

                // ✅ Clear existing options before appending new ones
                feederDropdown.innerHTML = ""; // Removes all previous options

                // ✅ Append new options from API data
                data.forEach(feeder => {
                    let option = document.createElement("option");
                    option.value = feeder.feederName; // Ensure correct property name
                    option.textContent = feeder.feederName; // Display name
                    feederDropdown.appendChild(option);
                });

                // ✅ Set the first feeder as the default selection
                let selectedFeeder = data[0].feederName;
                feederDropdown.value = selectedFeeder;

                     let selectedDate=   document.getElementById("dateFilter").value;
                // ✅ Debugging logs
                console.log("Selected Mill:", selectedMill);
                console.log("Selected Feeder:", selectedFeeder);

                // ✅ Call loadChartData after setting values
                        loadChartData(selectedMill, selectedFeeder,selectedDate);

                // ✅ Add event listener to update chart when Feeder changes
                feederDropdown.addEventListener("change", function () {
                    loadChartData(selectedMill, this.value,selectedDate);
                });

            } catch (error) {
                console.error("Error loading Feeder Names:", error);
            }
        }
                // Load chart on page load
               window.onload = function () 
               {
            // ✅ Set default date to today
            let today = new Date().toISOString().split('T')[0];
            let dateInput = document.getElementById("dateFilter");

            if (dateInput) {
                dateInput.value = today; // Set today's date
            } else {
                console.warn("Date filter input not found!");
            }

            // ✅ Load other data
           // loadChartData();
            loadMillname();
                };
 
        

        document.getElementById("millSelect").addEventListener("change", function () {
            let selectedMill = this.value || null;
                 
            loadFeedername(selectedMill);
                       let selectedFeeder=document.getElementById("FeederSelect").value;

                 //   loadChartData(selectedMill,selectedFeeder);
        });
    document.getElementById("FeederSelect").addEventListener("change", function () {
                    let selectedFeeder = this.value || null;
                            loadFeedername(selectedFeeder);
                           let selectedMill=document.getElementById("millSelect").value;

                            loadChartData(selectedMill,selectedFeeder);
                });
            function HomeLoad() {
            window.location.href = "/Home/Index";
        }
    </script>
</body>
</html>
