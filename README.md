const response = await fetch(url);
if (!response.ok) throw new Error(`HTTP error! Status: ${response.status}`);

const data = await response.json();
console.log("Fetched Data:", data);

const ctx = document.getElementById("kwhChart").getContext("2d");

// Function to clear the chart canvas
function clearChart() {
    if (window.myChart) {
        window.myChart.destroy(); // Destroy existing chart
    }
    ctx.clearRect(0, 0, ctx.canvas.width, ctx.canvas.height); // Clear canvas
}

if (!data || data.length === 0) {
    console.warn("No data available");
    clearChart(); // Clear the chart if data is empty
    return;
}

// If data exists, create or update the chart
if (window.myChart) {
    window.myChart.destroy(); // Destroy previous chart before creating a new one
}

window.myChart = new Chart(ctx, {
    type: "bar", // Change type as needed
    data: {
        labels: data.map((item) => item.label),
        datasets: [{
            label: "Energy Consumption (kWh)",
            data: data.map((item) => item.value),
            backgroundColor: "rgba(75, 192, 192, 0.2)",
            borderColor: "rgba(75, 192, 192, 1)",
            borderWidth: 1,
        }],
    },
    options: {
        responsive: true,
        scales: {
            y: {
                beginAtZero: true,
            },
        },
    },
});