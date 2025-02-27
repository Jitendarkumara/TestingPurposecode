ticks: {
    source: 'data',  // Use only actual data points
    autoSkip: false,  // Prevent automatic skipping
    maxRotation: 0,   // Prevents angled text
    minRotation: 0,   // Forces horizontal text
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
            return null; // Hides non-data points
        }
    }
}