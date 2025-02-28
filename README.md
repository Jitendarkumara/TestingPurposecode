scales: {
    x: {
        type: "time",
        time: {
            unit: "minute", // Adjust unit based on your data density
            tooltipFormat: "YYYY-MM-DD HH:mm:ss",
            displayFormats: {
                minute: "HH:mm",
                hour: "HH:mm",
                day: "MMM DD",
            },
        },
        grid: { color: "#eee" },
        title: { display: true, text: "Timestamp" },
        ticks: {
            autoSkip: false, // Show all points if needed
            maxRotation: 45, // Rotate labels to prevent overlap
            minRotation: 45, // Keep consistent rotation
        },
        distribution: "linear", // Spread points evenly
    },
}