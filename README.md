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

        console.log("Timestamps:", timestamps);
        console.log("KWH Data:", kwhData);

        let ctx = document.getElementById("kwhChart").getContext("2d");

        // Destroy previous chart instance if exists
        if (kwhChart !== null) {
            kwhChart.destroy();
        }

        // Create a new chart
        kwhChart = new Chart(ctx, {
            type: "line",
            data: {
                labels: timestamps,
                datasets: [{
                    label: "KWH",
                    data: kwhData,
                    borderColor: "blue",
                    fill: true
                }]
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
                        title: { display: true, text: "KWH" }
                    }
                }
            }
        });
    } catch (error) {
        console.error("Error loading chart data:", error);
    }
}

async function loadMillname() {
    try {
        let url = "/Home/GetAllMill";
        const response = await fetch(url);
        const data = await response.json();
        if (!data || data.length === 0) return;

        let millDropdown = document.getElementById("millSelect");
        millDropdown.innerHTML = "";
        data.forEach(mill => {
            let option = document.createElement("option");
            option.value = mill.allMillName;
            option.textContent = mill.allMillName;
            millDropdown.appendChild(option);
        });

        let selectedMill = data[0].allMillName;
        loadFeedername(selectedMill);
    } catch (error) {
        console.error("Error loading Mill Name:", error);
    }
}

async function loadFeedername(selectedMill) {
    try {
        let url = `/Home/GetFeeder?millName=${encodeURIComponent(selectedMill)}`;
        const response = await fetch(url);
        const data = await response.json();
        if (!data || data.length === 0) return;

        let feederDropdown = document.getElementById("FeederSelect");
        feederDropdown.innerHTML = "";
        data.forEach(feeder => {
            let option = document.createElement("option");
            option.value = feeder.feederName;
            option.textContent = feeder.feederName;
            feederDropdown.appendChild(option);
        });

        let selectedFeeder = data[0].feederName;
        feederDropdown.value = selectedFeeder;

        let selectedDate = document.getElementById("dateFilter").value;
        loadChartData(selectedMill, selectedFeeder, selectedDate);
    } catch (error) {
        console.error("Error loading Feeder Names:", error);
    }
}

window.onload = function () {
    let today = new Date().toISOString().split('T')[0];
    document.getElementById("dateFilter").value = today;
    loadMillname();
};

document.getElementById("millSelect").addEventListener("change", function () {
    loadFeedername(this.value);
});

document.getElementById("FeederSelect").addEventListener("change", function () {
    let selectedMill = document.getElementById("millSelect").value;
    let selectedFeeder = this.value;
    let selectedDate = document.getElementById("dateFilter").value;
    loadChartData(selectedMill, selectedFeeder, selectedDate);
});

document.getElementById("dateFilter").addEventListener("change", function () {
    let selectedMill = document.getElementById("millSelect").value;
    let selectedFeeder = document.getElementById("FeederSelect").value;
    let selectedDate = this.value;
    loadChartData(selectedMill, selectedFeeder, selectedDate);
});