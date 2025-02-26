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
                        }
                    },
                    scales: {
                        x: {

                            type: "time",
                            time: {
                               //  unit: "day",
                               unit: "day",
                         //displayFormats: { timestamp: "MMM d, yyyy HH:mm" }
                          displayFormats: { timestamp: "HH:mm" } ,
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
