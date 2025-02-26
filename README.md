ticks: {
    autoSkip: false,
    callback: function(value, index, values) {
        // Access KWH data from the dataset
        let kwhValue = kwhChart.data.datasets[0].data[index];

        // Show time only if KWH value exists (is not null or 0)
        if (kwhValue && kwhValue > 0) {
            return new Date(value).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', hour12: true });
        } else {
            return ""; // Hide time if no KWH data
        }
    }
}