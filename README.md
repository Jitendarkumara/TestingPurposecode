ticks: {
    callback: function(value, index, values) {
        let time = new Date(value).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', hour12: true });
        return index % 2 === 0 ? time : ""; // Show time only for every alternate index
    }
}