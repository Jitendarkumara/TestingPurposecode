async function loadMillname() {
    try {
        let url = "/Home/GetAllMill"; // API endpoint
        const response = await fetch(url);
        const data = await response.json();

        console.log("Fetched Data:", data);

        if (!data || data.length === 0) {
            alert("No Mill Name available!");
            return;
        }

        // Get the dropdown element
        let millDropdown = document.getElementById("millSelect");
        millDropdown.innerHTML = '<option value="">All Mills</option>'; // Default option

        // Add fetched mills to the dropdown
        data.forEach(mill => {
            let option = document.createElement("option");
            option.value = mill.Millname; // Assign value
            option.textContent = mill.AllMillName; // Assign display text
            millDropdown.appendChild(option);
        });

    } catch (error) {
        console.error("Error loading Mill Name:", error);
        alert("Failed to load Mill Name.");
    }
}

// Call the function to populate the dropdown when the page loads
loadMillname();