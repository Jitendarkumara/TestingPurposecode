x: {
    type: "time",
    time: {
        unit: "minute", 
        displayFormats: { minute: "hh:mm a" } 
    },
    grid: { color: "#eee" },
    title: { display: true, text: "Timestamp" },
    ticks: {
        autoSkip: false, // Prevents automatic skipping
        callback: function(value, index, values) {
            // Show label only at data points, hide others
            if (index % 2 === 0) {
                return new Date(value).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', hour12: true });
            } else {
                return ""; // Hide labels for other ticks
            }
        }
    }
}