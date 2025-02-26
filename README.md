ticks: {
    autoSkip: false, // Ensure no automatic skipping
    callback: function(value, index, values) {
        let kwhValue = kwhChart.data.datasets[0].data[index];

        // Show time only if KWH value exists
        if (kwhValue && kwhValue > 0) {
            return new Date(value).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', hour12: true });
        } else {
            return ""; // Hide time for missing or zero KWH values
        }
    }
}