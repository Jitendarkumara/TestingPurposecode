x: {
    type: "time",
    time: {
        unit: "minute",
        displayFormats: { minute: "hh:mm a" }
    },
    grid: { color: "#eee" },
    title: { display: true, text: "Timestamp" },
    ticks: {
        source: "data", // Show only real data points
        autoSkip: false,
        callback: function(value, index, values) {
            let kwhValue = kwhChart.data.datasets[0].data[index];
            return kwhValue > 0 ? new Date(value).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', hour12: true }) : "";
        }
    }
}