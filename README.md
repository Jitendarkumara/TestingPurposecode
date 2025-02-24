let kwhChart; // Store the chart instance

async function loadChartData(selectedMill = null) {
    try {
        let url = "/Home/GetKWHData"; // API endpoint

        const response = await fetch(url);
        const data = await response.json();

        console.log("Fetched Data:", data);

        if (!data || data.length === 0) {
            alert("No data available!");
            return;
        }

        // Filter data if a specific mill is selected
        let filteredData = selectedMill ? data.filter(item => item.millname.trim() === selectedMill) : data;

        if (filteredData.length === 0) {
            alert("No data found for the selected mill!");
            return;
        }

        // Ensure timestamps are properly formatted as JavaScript Date objects
        const timestamps = filteredData.map(item => new Date(item.timeStamp)); // Convert to Date object
        const kwhData = filteredData.map(item => parseFloat(item.kwh));

        console.log("Parsed Timestamps:", timestamps);
        console.log("KWH Data:", kwhData);

        let ctx = document.getElementById("kwhChart").getContext("2d");

        // Destroy existing chart instance to prevent duplication
        if (kwhChart) {
            kwhChart.destroy();
        }

        // Create a new chart
        kwhChart = new Chart(ctx, {
            type: "line",
            data: {
                labels: timestamps, // X-axis values
                datasets: [
                    {
                        label: "KWH",
                        data: kwhData,
                        borderColor: "blue",
                        backgroundColor: "rgba(0, 123, 255, 0.3)",
                        fill: true
                    }
                ]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                scales: {
                    x: {
                        type: "time", // Set x-axis as time-based
                        time: {
                            unit: "hour", // Change to 'day', 'minute', etc., if needed
                            tooltipFormat: "MMM d, yyyy HH:mm", // Format for tooltips
                            displayFormats: {
                                hour: "MMM d, HH:mm" // Format for x-axis labels
                            }
                        },
                        title: {
                            display: true,
                            text: "Timestamp"
                        },
                        ticks: {
                            autoSkip: true,
                            maxTicksLimit: 10 // Avoids cluttering x-axis
                        }
                    },
                    y: {
                        beginAtZero: true,
                        title: {
                            display: true,
                            text: "KWH Value"
                        }
                    }
                }
            }
        });
    } catch (error) {
        console.error("Error loading chart data:", error);
        alert("Failed to load data.");
    }
}

// Load chart on page load
loadChartData();

// Update chart when dropdown changes
document.getElementById("millSelect").addEventListener("change", function () {
    let selectedMill = this.value || null;
    loadChartData(selectedMill);
});

<script src="https://cdn.jsdelivr.net/npm/moment@2.29.1"></script>
<script src="https://cdn.jsdelivr.net/npm/chartjs-adapter-moment"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>