x: {
    type: "time",
    time: {
        unit: "minute", 
        displayFormats: { minute: "hh:mm a" } 
    },
    grid: { color: "#eee" },
    title: { display: true, text: "Timestamp" },
    ticks: {
        autoSkip: false, // Prevent Chart.js from automatically skipping ticks
        callback: function(value, index, values) {
            // Get the timestamp of the actual data point
            let dataPointTimes = values.map(v => v.value); 

            // Show label only if this tick corresponds to an actual data point
            if (dataPointTimes.includes(value)) {
                return new Date(value).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', hour12: true });
            } else {
                return ""; // Hide labels for other ticks
            }
        }
    }
}