x: {
    type: "time",
    time: {
        unit: "minute",
        displayFormats: { minute: "hh:mm a" }
    },
    grid: { color: "#eee" },
    title: { display: true, text: "Timestamp" },
    ticks: {
        source: 'data',  // <-- Use only actual data points
        autoSkip: false,
        callback: function(value, index, values) {
            let actualDataTimes = timestamps.map(t => new Date(t).getTime()); // Convert to milliseconds
            if (actualDataTimes.includes(value)) {
                return new Date(value).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', hour12: true });
            } else {
                return ""; // Hide non-data ticks
            }
        }
    }
}