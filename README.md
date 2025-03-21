async function loadMillname() {
    try {
        let url = "/Home/GetAllMill"; // API endpoint
        const response = await fetch(url);
        const data = await response.json();

        console.log("Fetched Data:", data); // Debugging

        if (!data || data.length === 0) {
            alert("No Mill Name available!");
            return;
        }

        // Get the dropdown element
        let millDropdown = document.getElementById("millSelect");

        // ✅ Clear previous options to avoid duplicates
        millDropdown.innerHTML = ""; 

        // ✅ Populate dropdown with mills from API
        data.forEach((mill, index) => {
            let option = document.createElement("option");
            option.value = mill.allMillName; // Ensure correct property name
            option.textContent = mill.allMillName;
            millDropdown.appendChild(option);
        });

        // ✅ Set the first mill as the default selection
        let selectedMill = data[0].allMillName;
        millDropdown.value = selectedMill;

        // ✅ Load feeders for the first selected mill
        loadFeedername(selectedMill);

        // ✅ Attach event listener to update feeders when the mill changes
        millDropdown.addEventListener("change", function () {
            loadFeedername(this.value);
        });

    } catch (error) {
        console.error("Error loading Mill Names:", error);
        alert("Failed to load Mill Names.");
    }
}