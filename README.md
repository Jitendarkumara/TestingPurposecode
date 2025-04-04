    <script>
        const mills = ["Finishing-2", "Ext-coating", "Finishing-1", "Mill2", "Mill1"];
        let charts = {}; // Store chart instances

        async function loadChartData(millName) {
            try {
                      let FromDate = document.getElementById("FromDate").value;
                let ToDate = document.getElementById("ToDate").value;
                let selectedShift = document.getElementById("shiftSelect").value;
                let selectedStatus = document.getElementById("statusSelect").value;

                if (!FromDate) {
                    alert("Please select a date.");
                    return;
                }

                let url = `/Home/GetKWHData?millName=${encodeURIComponent(millName)}&FromDate=${encodeURIComponent(FromDate)}&ToDate=${encodeURIComponent(ToDate)}&shiftSelect=${encodeURIComponent(selectedShift)}&RunningStatus=${encodeURIComponent(selectedStatus)}`;

                const response = await fetch(url);
                if (!response.ok) throw new Error(`HTTP error! Status: ${response.status}`);

                const data = await response.json();
                if (!data || data.length === 0) {
                    console.warn(`No data available for ${millName}`);
                    return;
                }

                let timestamps = data.map(item => new Date(item.timeStamp));
                let kwhData = data.map(item => parseFloat(item.kwh));
                let lslData = data.map(item => parseFloat(item.lsl));
                let uslData = data.map(item => parseFloat(item.usl));

                let ctx = document.getElementById(`chart-${millName}`).getContext("2d");

                // Destroy previous chart instance if exists
                if (charts[millName]) {
                    charts[millName].destroy();
                }

                // Create new chart
                charts[millName] = new Chart(ctx, {
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
                                borderDash: [5, 5]
                            },
                            {
                                label: "USL",
                                data: uslData,
                                borderColor: "red",
                                borderDash: [5, 5]
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
                console.error(`Error loading data for ${millName}:`, error);
            }
        }

        function createChartContainer(millName) {
            const container = document.getElementById("charts-container");
            const chartDiv = document.createElement("div");
            chartDiv.classList.add("chart-wrapper");

            chartDiv.innerHTML = `
                <div class="chart-title">${millName}</div>
                <canvas id="chart-${millName}"></canvas>
                <button class="chart-btn" onclick="loadChartData('${millName}')">Reload</button>
            `;

            container.appendChild(chartDiv);
        }

        function initializeCharts() {
            document.getElementById("FromDate").value = new Date().toISOString().split('T')[0]; // Set today's date
              document.getElementById("ToDate").value = new Date().toISOString().split('T')[0]; // Set today's date
            mills.forEach(mill => {
                createChartContainer(mill);
                loadChartData(mill);
            });
        }

        function refreshAllCharts() {
            document.getElementById("charts-container").innerHTML = ""; // Clear previous charts
            initializeCharts();
        }
         document.getElementById("btnSerch").addEventListener("click", function () {
            
           mills.forEach(mill => {
                createChartContainer(mill);
                loadChartData(mill);
            });
        });

        window.onload = initializeCharts;
    </script>
