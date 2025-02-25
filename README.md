scales: {
    x: {
        type: "linear", // Change to linear because kWh values are numerical
        title: { display: true, text: "KWH Consumption" }, // X-axis label
        grid: { color: "#eee" }
    },
    y: {
        type: "time", // Use time scale for timestamps
        time: {
            unit: "minute", // Adjust as needed (minute, hour, day)
            displayFormats: {
                minute: "MMM d, yyyy HH:mm",
                hour: "MMM d, yyyy HH:mm",
                day: "MMM d, yyyy HH:mm"
            }
        },
        title: { display: true, text: "Timestamp" }, // Y-axis label
        grid: { color: "#ddd" }
    }
}