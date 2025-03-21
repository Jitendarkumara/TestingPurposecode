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
     /*     millDropdown.innerHTML = '<option value="Mill1">Mill1</option>'; */ // Default option

         // Add fetched mills to the dropdown
         data.forEach(mill => {
             let option = document.createElement("option");
             option.value = mill.allMillName; // Correct property name
             option.textContent = mill.allMillName; // Display name
             millDropdown.appendChild(option);
         });
                 let selectedMill=data[0].allMillName;
          loadFeedername(selectedMill);

     } catch (error) {
         console.error("Error loading Mill Name:", error);
       //  alert("Failed to load Mill Name.");
     }
 }
