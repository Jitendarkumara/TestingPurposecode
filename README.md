async function loadFeedername(selectedMill) {
    try {
        let url = "/Home/GetFeeder"; // API endpoint
        if (selectedMill) {
            url += `?millName=${encodeURIComponent(selectedMill)}`;
        }

        const response = await fetch(url);
        const data = await response.json();

        console.log("Fetched Feeder Data:", data); // Debugging

        if (!data || data.length === 0) {
            console.warn("No Feeder available!");
            return;
        }

        // Get the dropdown element
        let feederDropdown = document.getElementById("FeederSelect");

        // ✅ Clear existing options before appending new ones
        feederDropdown.innerHTML = ""; // Removes all previous options

        // ✅ Append new options from API data
        data.forEach(feeder => {
            let option = document.createElement("option");
            option.value = feeder.feederName; // Ensure correct property name
            option.textContent = feeder.feederName; // Display name
            feederDropdown.appendChild(option);
        });

        // ✅ Set the first feeder as the default selection
        let selectedFeeder = data[0].feederName;
        feederDropdown.value = selectedFeeder;

        // ✅ Debugging logs
        console.log("Selected Mill:", selectedMill);
        console.log("Selected Feeder:", selectedFeeder);

        // ✅ Call loadChartData after setting values
        loadChartData(selectedMill, selectedFeeder);

        // ✅ Add event listener to update chart when Feeder changes
        feederDropdown.addEventListener("change", function () {
            loadChartData(selectedMill, this.value);
        });

    } catch (error) {
        console.error("Error loading Feeder Names:", error);
    }
}