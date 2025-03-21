    async function loadFeedername(selectedMill) {
    try {
        let url = "/Home/GetFeeder"; // API endpoint
        if (selectedMill) {
            url += `?millName=${encodeURIComponent(selectedMill)}`;
        }

        const response = await fetch(url);
        const data = await response.json();

        console.log("Fetched Data:", data); // Debugging

        if (!data || data.length === 0) {
          //  alert("No Feeder is available!");
            return;
        }

        // Get the dropdown element
        let feederDropdown = document.getElementById("FeederSelect");

        // ✅ Clear existing options before appending new ones
        feederDropdown.innerHTML = ""; // Removes all previous options

        // ✅ Optional: Add a default option at the top
      //  let defaultOption = document.createElement("option");
       // defaultOption.value = "";
       // defaultOption.textContent = "-- Select Feeder --";
        //feederDropdown.appendChild(defaultOption);

        // ✅ Append new options from API data
        data.forEach(feeder => {
            let option = document.createElement("option");
            option.value = feeder.feederName; // Correct property name
            option.textContent = feeder.feederName; // Display name
            feederDropdown.appendChild(option);
        });
                let selectedMill=document.getElementById("millSelect").value;
                      let selectedFeeder =data[0].feederName;
                                      conslole.log(selectedMill,selectedFeeder)
                loadChartData(selectedMill,selectedFeeder)

    } catch (error) {
        console.error("Error loading Feeder Names:", error);
     //   alert("Failed to load Feeder Names.");
    }
}
