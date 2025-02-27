ticks: {
    source: 'data',  // Use only actual data points
    autoSkip: true,  // Allow automatic skipping to prevent overlap
    autoSkipPadding: 20, // Adds padding to prevent excessive skips
    maxRotation: 45,  // Rotate labels to avoid overlapping
    minRotation: 30,  // Ensures labels are always rotated slightly
    callback: function(value, index, values) {
        let actualDataTimes = timestamps.map(t => new Date(t).getTime()); // Convert to milliseconds
        if (actualDataTimes.includes(value)) {
            return new Date(value).toLocaleString([], {
                year: 'numeric',
                month: 'short',
                day: '2-digit',
                hour: '2-digit',
                minute: '2-digit',
                second: '2-digit',
                hour12: true
            });
        } else {
            return ""; // Hide non-data points properly
        }
    }
}