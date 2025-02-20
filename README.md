async function loadChartData() {
    const response = await fetch("/Home/GetKWHData");
    const data = await response.json();
    
    console.log("Fetched Data:", data); // ✅ Check if data is correct

    if (!data || data.length === 0) {
        alert("No data available!"); // ✅ Alert if no data is returned
        return;
    }

    // Fix property names (case-sensitive)
    const millNames = data.map(item => item.millname.trim()); // ✅ Use "millname" (lowercase)
    const kwhData = data.map(item => parseFloat(item.kwh)); // ✅ Use "kwh" (lowercase)

    console.log("Mill Names:", millNames);
    console.log("KWH Data:", kwhData);

    // Fixed LSL & USL values (use correct length)
    const lslData = new Array(millNames.length).fill(10); // Example LSL
    const uslData = new Array(millNames.length).fill(50); // Example USL

    // Create chart
    var ctx = document.getElementById("kwhChart").getContext("2d");

    new Chart(ctx, {
        type: "line",
        data: {
            labels: millNames,
            datasets: [
                {
                    label: "KWH",
                    data: kwhData,
                    borderColor: "blue",
                    backgroundColor: "rgba(0, 123, 255, 0.3)",
                    fill: true
                },
                {
                    label: "LSL",
                    data: lslData,
                    borderColor: "orange",
                    borderDash: [5, 5],
                    fill: false
                },
                {
                    label: "USL",
                    data: uslData,
                    borderColor: "gray",
                    borderDash: [5, 5],
                    fill: false
                }
            ]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            aspectRatio: 2,
            plugins: {
                tooltip: {
                    callbacks: {
                        label: function (tooltipItem) {
                            let datasetLabel = tooltipItem.dataset.label || "";
                            let value = tooltipItem.raw;
                            let millName = millNames[tooltipItem.dataIndex];
                            return `${millName} - ${datasetLabel}: ${value}`;
                        }
                    }
                }
            },
            scales: {
                y: { beginAtZero: true, grid: { color: "#ddd" }, title: { display: true, text: "KWH Value" } },
                x: { grid: { color: "#eee" }, title: { display: true, text: "Mill Names" } }
            }
        }
    });
}

// Load chart on page load
loadChartData();