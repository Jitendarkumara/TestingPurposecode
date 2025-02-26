ticks: {
    source: 'data',  // Use only actual data points
    autoSkip: false,
    callback: function(value, index, values) {
        let actualDataTimes = timestamps.map(t => new Date(t).getTime()); // Convert to milliseconds

        // Ensure that only real KWH data points are shown
        if (actualDataTimes.includes(value)) {
            return new Date(value).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', second: '2-digit', hour12: true });
        } else {
            return null; // Hide other timestamps
        }
    }
}