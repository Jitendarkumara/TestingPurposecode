async function loadChartData(selectedMill = "Mill1", selectedFeeder = "All") {
    try {
        let url = `/Home/GetKWHData?millName=${encodeURIComponent(selectedMill)}&feederName=${encodeURIComponent(selectedFeeder)}`;

        // Fetch data from the API
        let response = await fetch(url);
        let data = await response.json();

        // Handle the received data (e.g., update the chart)
        console.log(data);
    } catch (error) {
        console.error("Error loading chart data:", error);
    }
}