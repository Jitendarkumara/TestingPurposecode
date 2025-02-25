scales: {
    x: {
        type: "time",
        time: {
            unit: "minute", // Adjust based on your data (minute, hour, day)
            tooltipFormat: "MMM d, yyyy HH:mm", // Format for tooltip
            displayFormats: {
                minute: "MMM d, yyyy HH:mm",
                hour: "MMM d, yyyy HH:mm",
                day: "MMM d, yyyy HH:mm"
            }
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