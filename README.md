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
                @* <div class="col-md-2">
                    <label for="FeederSelect" class="fw-bold">Select Feeder</label>
                    <select id="FeederSelect" class="form-select m-0"></select>
                </div> *@
                <div class="col-md-2">
                    <label for="dateFilter" class="fw-bold">Select Date</label>
                    <input type="date" id="dateFilter" class="form-control m-0">
                </div>
                <div class="col-md-2">
                    <label for="shiftSelect" class="fw-bold">Select Shift</label>
                    <select id="shiftSelect" class="form-select m-0">
                    @*     <option selected disabled>Select Shift</option> *@
                        <option value="All">All</option>
                        <option value="A">Shift A</option>
                        <option value="B">Shift B</option>
                        <option value="C">Shift C</option>
                    
                    </select>
                </div>
                <div class="col-md-2">
                    <label for="statusSelect" class="fw-bold">Select Status</label>
                    <select id="statusSelect" class="form-select m-0">
                       
                        <option value="All">All</option>
                        <option value="1">Running</option>
                        <option value="0">Stop</option>
                    </select>
                </div>
            </div>
        </div>
    </div>

    @* <div id="chart-container" style="margin-top:3px">
        <canvas id="kwhChart"></canvas>
    </div> *@
    <div style="position: relative; width: 600px; height: 300px;margin-top:3px;border:2px solid black">
        <canvas id="kwhChart"></canvas>
        <button id="chartButton" style="position: absolute; top: 10px; right: 10px; z-index: 10;">Click</button>
    </div>
    <div style="position: relative; width: 600px; height: 300px;margin-top:3px;border:2px solid black">
        <canvas id="kwhChart1"></canvas>
        <button id="chartButton" style="position: absolute; top: 10px; right: 10px; z-index: 10;">Click</button>
    </div>
    <script>
        let kwhChart=null;

       

        async function loadChartData(selectedMill = "Mill1") {
            try {
                let selectedDate = document.getElementById("dateFilter").value;
                   let Runningstatus = document.getElementById("statusSelect").value;
                   let shiftselect= document.getElementById("shiftSelect").value;
                  
                console.log("Selected Date:", selectedDate);

               // let url = `/Home/GetKWHData?millName=${encodeURIComponent(selectedMill)}&feederName=${encodeURIComponent(selectedFeeder)}&selectedDate=${encodeURIComponent(selectedDate)}&RunningStatus=${encodeURIComponent(Runningstatus)}&shiftSelect=${encodeURIComponent(shiftselect)}`;
               let url = `/Home/GetKWHData?millName=${encodeURIComponent(selectedMill)}&selectedDate=${encodeURIComponent(selectedDate)}&RunningStatus=${encodeURIComponent(Runningstatus)}&shiftSelect=${encodeURIComponent(shiftselect)}`;

                const response = await fetch(url);
                if (!response.ok) throw new Error(`HTTP error! Status: ${response.status}`);

                const data = await response.json();
                console.log("Fetched Data:", data);

                if (!data || data.length === 0) {
                    console.warn("No data available");
                
              clearChart();
                  alert("data is not available");
                    return;
                }

                let timestamps = data.map(item => new Date(item.timeStamp));
                let kwhData = data.map(item => parseFloat(item.kwh));
                let lslData = data.map(item => parseFloat(item.lsl));
                let uslData = data.map(item => parseFloat(item.usl));

                console.log("KWH Data:", kwhData);
                console.log("LSL Data:", lslData);
                console.log("USL Data:", uslData);

                let ctx = document.getElementById("kwhChart").getContext("2d");

                // Destroy previous chart instance if exists
                if (kwhChart !== null) {
                    kwhChart.destroy();
                }

                // Create a new chart with KWH, LSL, and USL
                kwhChart = new Chart(ctx, {
                    type: "line",
                    data: {
                        labels: timestamps,
                        datasets: [
                            {
                                label: "KWH",
                                data: kwhData,
                                borderColor: "blue",
                                fill: false
                            },
                            {
                                label: "LSL",
                                data: lslData,
                                borderColor: "green",
                                borderDash: [5, 5], // Dotted line for LSL
                                fill: false
                            },
                            {
                                label: "USL",
                                data: uslData,
                                borderColor: "red",
                                borderDash: [5, 5], // Dotted line for USL
                                fill: false
                            }
                        ]
                    },
                    options: {
                        responsive: true,
                        scales: {
                            x: {
                                type: "time",
                                time: { unit: "minute" },
                                title: { display: true, text: "Timestamp" }
                            },
                            y: {
                                title: { display: true, text: "KWH / LSL / USL" }
                            }
                        }
                    }
                });
            } catch (error) {
                console.error("Error loading chart data:", error);
            }
        }

        async function loadMillname() {
            try {
                let url = "/Home/GetAllMill";
                const response = await fetch(url);
                const data = await response.json();
                if (!data || data.length === 0) return;

                let millDropdown = document.getElementById("millSelect");
                //millDropdown.innerHTML = "";
               // millDropdown.innerHTML = '<option selected disabled>Select Line</option>';
                // data.sort((a,b) => a.allMillName.localeCompare(b.allMillName, undefined,{ sensitivity:'base'}));
                data.forEach(mill => {
                    let option = document.createElement("option");
                    option.value = mill.allMillName;
                    option.textContent = mill.allMillName;
                    millDropdown.appendChild(option);
                });

               // let selectedMill = data[0].allMillName;
               // loadFeedername(selectedMill);
            } catch (error) {
                console.error("Error loading Mill Name:", error);
            }
        }

        async function loadFeedername(selectedMill) {
            try {
                let url = `/Home/GetFeeder?millName=${encodeURIComponent(selectedMill)}`;
                const response = await fetch(url);
                const data = await response.json();
                if (!data || data.length === 0) return;

                let feederDropdown = document.getElementById("FeederSelect");
                feederDropdown.innerHTML = "";
                data.forEach(feeder => {
                    let option = document.createElement("option");
                    option.value = feeder.feederName;
                    option.textContent = feeder.feederName;
                    feederDropdown.appendChild(option);
                });

                let selectedFeeder = data[0].feederName;
                feederDropdown.value = selectedFeeder;

                let selectedDate = document.getElementById("dateFilter").value;
                 let Runningstatus = document.getElementById("statusSelect").value;
                loadChartData(selectedMill, selectedFeeder, selectedDate,Runningstatus);
            } catch (error) {
                console.error("Error loading Feeder Names:", error);
            }
        }

        window.onload = function () {
            let today = new Date().toISOString().split('T')[0];
            document.getElementById("dateFilter").value = today;
            loadMillname();
        };

        document.getElementById("millSelect").addEventListener("change", function () {
          //  loadFeedername(this.value);
            loadChartData(this.value);

        });

        // document.getElementById("FeederSelect").addEventListener("change", function () {
        //     let selectedMill = document.getElementById("millSelect").value;
        //     let selectedFeeder = this.value;
        //     let selectedDate = document.getElementById("dateFilter").value;
        //       let Runningstatus = document.getElementById("statusSelect").value;
        //     loadChartData(selectedMill, selectedFeeder, selectedDate,Runningstatus);
        // });

        document.getElementById("dateFilter").addEventListener("change", function () {
            let selectedMill = document.getElementById("millSelect").value;
            // let selectedFeeder = document.getElementById("FeederSelect").value;
             let Runningstatus = document.getElementById("statusSelect").value;
            let selectedDate = this.value;
            loadChartData(selectedMill, selectedDate,Runningstatus);
        });

          document.getElementById("statusSelect").addEventListener("change", function () {
            let selectedMill = document.getElementById("millSelect").value;
           // let selectedFeeder = document.getElementById("FeederSelect").value;
            let selectedDate = document.getElementById("dateFilter").value;
            let Runningstatus = this.value;
            loadChartData(selectedMill, selectedDate,Runningstatus);
        });
             document.getElementById("shiftSelect").addEventListener("change", function () {
            let selectedMill = document.getElementById("millSelect").value;
           // let selectedFeeder = document.getElementById("FeederSelect").value;
           // let selectedDate = document.getElementById("dateFilter").value;
           // let Runningstatus = this.value;
            loadChartData(selectedMill);
        });
              

        // Function to clear the chart canvas
        function clearChart() {
              const ctx = document.getElementById("kwhChart").getContext("2d");
            if (window.myChart) {
                window.myChart.destroy(); // Destroy existing chart
            }
            ctx.clearRect(0, 0, ctx.canvas.width, ctx.canvas.height); // Clear canvas
        }
    </script>
</body>
</html>

<select id="millSelect" class="form-select m-0"><option value="Finishing-2">Finishing-2</option><option value="Ext-coating">Ext-coating</option><option value="Finishing-1">Finishing-1</option><option value="Mill2">Mill2</option><option value="Mill1">Mill1</option></select>
