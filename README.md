x: {
    type: "time",
    time: {
        unit: "hour", 
        displayFormats: { hour: "HH:mm" } 
    },
    grid: { color: "#eee" },
    title: { display: true, text: "Timestamp" },
    ticks: {
        callback: function(value) {
            return new Date(value).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }); 
        }
    }
}