options: {
    responsive: true,
    maintainAspectRatio: false,
    interaction: {
        mode: 'x', // Ensure interaction is limited to X-axis
    },
    plugins: {
        tooltip: {
            callbacks: {
                label: function (tooltipItem) {
                    let datasetLabel = tooltipItem.dataset.label || "";
                    let value = tooltipItem.raw;
                    let timestamp = new Date(timestamps[tooltipItem.dataIndex]).toLocaleString();
                    return `${datasetLabel}: ${value}`;
                }
            }
        },
        zoom: {
            pan: {
                enabled: true,
                mode: "x", // Enable horizontal scrolling
                rangeMin: {
                    x: null // Allows scrolling left
                },
                rangeMax: {
                    x: null // Allows scrolling right
                },
                onPan: function({chart}) {
                    console.log('Panning', chart);
                }
            },
            zoom: {
                wheel: {
                    enabled: true,
                    mode: "x" // Zoom only on x-axis
                },
                pinch: {
                    enabled: true
                },
                mode: "x",
                onZoom: function({chart}) {
                    console.log('Zooming', chart);
                }
            }
        }
    },
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
                source: 'data',  
                autoSkip: true,  
                autoSkipPadding: 20, 
                maxRotation: 45,  
                minRotation: 30,  
                callback: function(value) {
                    return new Date(value).toLocaleString([], {
                        year: 'numeric',
                        month: 'short',
                        day: '2-digit',
                        hour: '2-digit',
                        minute: '2-digit',
                        second: '2-digit',
                        hour12: true
                    });
                }
            }
        }
    }
}