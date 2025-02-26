x: {
    type: "time",
    time: {
        unit: "minute", // Ensure we're working with minutes
        displayFormats: { minute: "hh:mm:ss a" } // Show hours, minutes, and seconds
    },
    grid: { color: "#eee" },
    title: { display: true, text: "Timestamp" },
    ticks: {
        autoSkip: false, // Prevent auto-skipping
        callback: function(value, index, values) {
            let dataPointTimes = timestamps.map(t => t.getTime()); // Get actual timestamps from dataset

            if (dataPointTimes.includes(value)) {
                return new Date(value).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', second: '2-digit', hour12: true });
            } else {
                return ""; // Hide non-data points
            }
        }
    }
}