options: {
    responsive: true,
    maintainAspectRatio: false,
    scales: {
        x: {
            type: "time",
            time: {
                unit: "minute",
                displayFormats: { minute: "hh:mm a" }
            },
            grid: { color: "#eee" },
            title: { display: true, text: "Timestamp" },
            ticks: {
                source: "data",
                autoSkip: true,
                maxRotation: 45,
                minRotation: 30,
                callback: function (value, index, values) {
                    return new Date(value).toLocaleString([], {
                        year: "numeric",
                        month: "short",
                        day: "2-digit",
                        hour: "2-digit",
                        minute: "2-digit",
                        second: "2-digit",
                        hour12: true
                    });
                }
            }
        }
    },
    plugins: {
        zoom: {
            pan: {
                enabled: true,
                mode: "x", // Allow horizontal scrolling
                speed: 10
            },
            zoom: {
                wheel: {
                    enabled: true,
                    mode: "x" // Zoom only on x-axis
                },
                pinch: {
                    enabled: true
                },
                mode: "x"
            }
        }
    }
}