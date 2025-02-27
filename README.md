                        options: {
                            responsive: true,
                            maintainAspectRatio: false,
                            plugins: {
                                tooltip: {
                                    callbacks: {
                                        label: function (tooltipItem) {
                                            let datasetLabel = tooltipItem.dataset.label || "";
                                            let value = tooltipItem.raw;
                                            let timestamp = new Date(timestamps[tooltipItem.dataIndex]).toLocaleString();
                                            // return `${timestamp} - ${datasetLabel}: ${value}`;
                                            return `${datasetLabel}: ${value}`;
                                        }
                                    }
                                        }, zoom: {
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
//-------------------only time------------------------
            // ticks: {
            //     source: 'data',  // <-- Use only actual data points
            //     autoSkip: false,
            //     callback: function(value, index, values) {
            //         let actualDataTimes = timestamps.map(t => new Date(t).getTime()); // Convert to milliseconds
            //         if (actualDataTimes.includes(value)) {
            //             return new Date(value).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', hour12: true });
            //         } else {
            //             return ""; // Hide non-data ticks
            //         }
            //     }
            // }
 //-------------date with time ----------------------
        //         ticks: {
        //     source: 'data',  // Use only actual data points
        //     autoSkip: false,  // Prevent automatic skipping
        //     maxRotation: 0,   // Prevents angled text
        //     minRotation: 0,   // Forces horizontal text
        //     callback: function(value, index, values) {
        //         let actualDataTimes = timestamps.map(t => new Date(t).getTime()); // Convert to milliseconds
        //         if (actualDataTimes.includes(value)) {
        //             return new Date(value).toLocaleString([], {
        //                 year: 'numeric',
        //                 month: 'short',
        //                 day: '2-digit',
        //                 hour: '2-digit',
        //                 minute: '2-digit',
        //                 second: '2-digit',
        //                 hour12: true
        //             });
        //         } else {
        //             return null; // Hides non-data points
        //         }
        //     }
        // }

                           
//---------------Testing code--------------------

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

//-----------------------------------
        }

              
                            }
                        }
