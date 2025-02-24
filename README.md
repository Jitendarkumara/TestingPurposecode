let kwhChart; // Store the chart instance

async function loadChartData(selectedMill = null) {
    try {
        let url = "/Home/GetKWHData"; // API endpoint
        const response = await fetch(url);
        const result = await response.json();

        const data = result.chartData; // Extract chart data
        const distinctMills = result.mills; // Extract unique mills

        console.log("Fetched Data:", data);
        console.log("Distinct Mills:", distinctMills);

        if (!data || data.length === 0) {
            alert("No data available!");
            return;
        }

        // Populate the mill dropdown with distinct values (only once)
        let dropdown = document.getElementById("millSelect");
        dropdown.innerHTML = '<option value="">All Mills</option>'; // Default option

        distinctMills.forEach(mill => {
            let option = document.createElement("option");
            option.value = mill;
            option.textContent = mill;
            dropdown.appendChild(option);
        });

        // Filter data if a specific mill is selected
        let filteredData = selectedMill ? data.filter(item => item.millname.trim() === selectedMill) : data;

        if (filteredData.length === 0) {
            alert("No data found for the selected mill!");
            return;
        }

        // Convert timestamps to Date objects
        const timestamps = filteredData.map(item => new Date(item.timeStamp)); // Ensure Date format
        const kwhData = filteredData.map(item => parseFloat(item.kwh));

        let ctx = document.getElementById("kwhChart").getContext("2d");

        // Destroy existing chart to prevent duplication
        if (kwhChart) {
            kwhChart.destroy();
        }

        // Create a new chart
        kwhChart = new Chart(ctx, {
            type: "line",
            data: {
                labels: timestamps, // X-axis values
                datasets: [{
                    label: "KWH",
                    data: kwhData,
                    borderColor: "blue",
                    backgroundColor: "rgba(0, 123, 255, 0.3)",
                    fill: true
                }]
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
                        title: { display: true, text: "Timestamp" },
                        ticks: { autoSkip: true, maxTicksLimit: 10 }
                    },
                    y: {
                        beginAtZero: true,
                        title: { display: true, text: "KWH Value" }
                    }
                }
            }
        });
    } catch (error) {
        console.error("Error loading chart data:", error);
        alert("Failed to load data.");
    }
}

// Load chart and dropdown on page load
loadChartData();

// Update chart when dropdown changes
document.getElementById("millSelect").addEventListener("change", function () {
    let selectedMill = this.value || null;
    loadChartData(selectedMill);
});