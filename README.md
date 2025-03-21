let kwhChart = null; // Define chart globally

async function loadChartData(selectedMill = "Mill1", selectedFeeder = "All") {
    try {
        let selectedDate = document.getElementById("dateFilter").value;
        console.log("Selected Date:", selectedDate);

        let url = `/Home/GetKWHData?millName=${encodeURIComponent(selectedMill)}&feederName=${encodeURIComponent(selectedFeeder)}&selectedDate=${encodeURIComponent(selectedDate)}`;

        const response = await fetch(url);
        if (!response.ok) throw new Error(`HTTP error! Status: ${response.status}`);

        const data = await response.json();
        console.log("Fetched Data:", data);

        if (!data || data.length === 0) {
            console.warn("No data available");
            return;
        }

        let timestamps = data.map(item => new Date(item.timeStamp));
        let kwhData = data.map(item => parseFloat(item.kwh));
        let lslData = data.map(item => parseFloat(item.lsl));
        let uslData = data.map(item => parseFloat(item.usl));

        console.log("KWH Data:", kwhData);
        console.log("LSL Data:", lslData);
        console.log("USL Data:", uslData);

        let ctx = document.getElementById("kwhChart").getContext("2d");

        // Destroy previous chart instance if exists
        if (kwhChart !== null) {
            kwhChart.destroy();
        }

        // Create a new chart with KWH, LSL, and USL
        kwhChart = new Chart(ctx, {
            type: "line",
            data: {
                labels: timestamps,
                datasets: [
                    {
                        label: "KWH",
                        data: kwhData,
                        borderColor: "blue",
                        fill: false
                    },
                    {
                        label: "LSL",
                        data: lslData,
                        borderColor: "green",
                        borderDash: [5, 5], // Dotted line for LSL
                        fill: false
                    },
                    {
                        label: "USL",
                        data: uslData,
                        borderColor: "red",
                        borderDash: [5, 5], // Dotted line for USL
                        fill: false
                    }
                ]
            },
            options: {
                responsive: true,
                scales: {
                    x: {
                        type: "time",
                        time: { unit: "minute" },
                        title: { display: true, text: "Timestamp" }
                    },
                    y: {
                        title: { display: true, text: "KWH / LSL / USL" }
                    }
                }
            }
        });
    } catch (error) {
        console.error("Error loading chart data:", error);
    }
}