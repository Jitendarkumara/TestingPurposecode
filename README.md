<body>
    <header b-276fsxalof="">
        <h4 b-276fsxalof="" class="text-center" style="background-color:cornflowerblue">EMS DashBoard</h4>
    </header>
    <div b-276fsxalof="" class="container">
        <main b-276fsxalof="" role="main" class="pb-3">
            




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
    </style>


    <div> </div>

    <div style="float:right">       <button onclick="HomeLoad()">Home</button></div>

    <label for="millSelect">Select Mill:</label>
    <select id="millSelect"><option value="Mill1">Mill1</option><option value="Ect_coating">Ect_coating</option><option value="Finishing2">Finishing2</option><option value="Ext-coating">Ext-coating</option><option value="Finishing1">Finishing1</option><option value="Mill2">Mill2</option><option value="Mill1">Mill1</option></select>

    <div id="chart-container">
        <canvas id="kwhChart" width="656" height="560" style="display: block; box-sizing: border-box; height: 560px; width: 656px;"></canvas>
    </div>

    <script>
        let kwhChart; // Store the chart instance

               async function loadChartData(selectedMill="Mill1") {
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
//                         options: {
//                             responsive: true,
//                             maintainAspectRatio: false,
//                             plugins: {
//                                 tooltip: {
//                                     callbacks: {
//                                         label: function (tooltipItem) {
//                                             let datasetLabel = tooltipItem.dataset.label || "";
//                                             let value = tooltipItem.raw;
//                                             let timestamp = new Date(timestamps[tooltipItem.dataIndex]).toLocaleString();
//                                             // return `${timestamp} - ${datasetLabel}: ${value}`;
//                                             return `${datasetLabel}: ${value}`;
//                                         }
//                                     }
//                                         }, zoom: {
//                     pan: {
//                         enabled: true,
//                         mode: "x", // Allow horizontal scrolling
//                         speed: 10
//                     },
//                     zoom: {
//                         wheel: {
//                             enabled: true,
//                             mode: "x" // Zoom only on x-axis
//                         },
//                         pinch: {
//                             enabled: true
//                         },
//                         mode: "x"
//                     }
//                 }


//                             },
//                             scales: {
// x: {
//             type: "time",
//             time: {
//                 unit: "minute",
//                 displayFormats: { minute: "hh:mm a" }
//             },
//             grid: { color: "#eee" },
//             title: { display: true, text: "Timestamp" },
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

//         ticks: {
//             source: 'data',  // Use only actual data points
//             autoSkip: true,  // Allow automatic skipping to prevent overlap
//             autoSkipPadding: 20, // Adds padding to prevent excessive skips
//             maxRotation: 45,  // Rotate labels to avoid overlapping
//             minRotation: 30,  // Ensures labels are always rotated slightly
//             callback: function(value, index, values) {
//                 let actualDataTimes = timestamps.map(t => new Date(t).getTime()); // Convert to milliseconds
//                 if (actualDataTimes.includes(value)) {
//                     return new Date(value).toLocaleString([], {
//                         year: 'numeric',
//                         month: 'short',
//                         day: '2-digit',
//                         hour: '2-digit',
//                         minute: '2-digit',
//                         second: '2-digit',
//                         hour12: true
//                     });
//                 } else {
//                     return ""; // Hide non-data points properly
//                 }
//             }
//         }

// //-----------------------------------
//         }

              
//                             }
//                         }

//-----------------------End of Plugins

        options: {
            responsive: true,
            maintainAspectRatio: false,
            interaction: {
                mode: 'x', // Ensure interaction is limited to X-axis
            },
            plugins: {
                tooltip: {
                    callbacks: {
                        label: function (tooltipItem) {
                            let datasetLabel = tooltipItem.dataset.label || "";
                            let value = tooltipItem.raw;
                            let timestamp = new Date(timestamps[tooltipItem.dataIndex]).toLocaleString();
                            return `${datasetLabel}: ${value}`;
                        }
                    }
                },
                zoom: {
                    pan: {
                        enabled: true,
                        mode: "x", // Enable horizontal scrolling
                        rangeMin: {
                            x: null // Allows scrolling left
                        },
                        rangeMax: {
                            x: null // Allows scrolling right
                        },
                        onPan: function({chart}) {
                            console.log('Panning', chart);
                        }
                    },
                    zoom: {
                        wheel: {
                            enabled: true,
                            mode: "x" // Zoom only on x-axis
                        },
                        pinch: {
                            enabled: true
                        },
                        mode: "x",
                        onZoom: function({chart}) {
                            console.log('Zooming', chart);
                        }
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
                    ticks: {
                        source: 'data',
                        autoSkip: true,
                        autoSkipPadding: 20,
                        maxRotation: 45,
                        minRotation: 30,
                        callback: function(value) {
                            return new Date(value).toLocaleString([], {
                                year: 'numeric',
                                month: 'short',
                                day: '2-digit',
                                hour: '2-digit',
                                minute: '2-digit',
                                second: '2-digit',
                                hour12: true
                            });
                        }
                    }
                }
            }
        }



//---------------------------------------
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
                millDropdown.innerHTML = '<option value="Mill1">Mill1</option>'; // Default option

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
        window.onload=function()
        {
        loadChartData();
        loadMillname();
        }
 
        

        document.getElementById("millSelect").addEventListener("change", function () {
            let selectedMill = this.value || null;
            loadChartData(selectedMill);
        });
            function HomeLoad() {
            window.location.href = "/Home/Index";
        }
    </script>
<script src="/_framework/aspnetcore-browser-refresh.js"></script>

        </main> 
    </div>

    <footer b-276fsxalof="" class="border-top footer text-muted">
        <div b-276fsxalof="" class="container">
            © 2025 - EmsApplication - <a href="/Home/Privacy">Privacy</a>
        </div>
    </footer>
    <script src="/lib/jquery/dist/jquery.min.js"></script>
    <script src="/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
    <script src="/js/site.js?v=hRQyftXiu1lLX2P9Ly9xa4gHJgLeR1uGN5qegUobtGo"></script>
    

<!-- Visual Studio Browser Link -->
<script type="text/javascript" src="/_vs/browserLink" async="async" id="__browserLink_initializationData" data-requestid="1a9894a0123347f28ea7f65b59ca2669" data-requestmappingfromserver="false" data-connecturl="http://localhost:63332/bdb065fc1f0b4e86888d51a62cd26528/browserLink"></script>
<!-- End Browser Link -->


</body>
