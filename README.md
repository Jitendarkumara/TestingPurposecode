async function loadFeedername(selectedMill="Mill1") {
               try {
                         //  alert(selectedMill);
                   

                   let url = "/Home/GetFeeder"; // API endpoint
                           if (selectedMill) {
                               url += `?millName=${encodeURIComponent(selectedMill)}`;
                           }
                   const response = await fetch(url);
                   const data = await response.json();

                   console.log("Fetched Data:", data); // Debugging

                   if (!data || data.length === 0) {
                       //alert("No Feeder is available!"); Saurabh
                       return;
                   }
                 
                   // Get the dropdown element
                   let feederDropdown = document.getElementById("FeederSelect");
                  /*  millDropdown.innerHTML = '<option value="All">All</option>';  */// Default option
                  console.log()
                   // Add fetched mills to the dropdown
                   data.forEach(feeder => {
                       let option = document.createElement("option");
                                       option.value = feeder.feederName; // Correct property name
                                       option.textContent = feeder.feederName; // Display name
                               feederDropdown.appendChild(option);
                   });

               } catch (error) {
                   console.error("Error loading Mill Name:", error);
                   alert("Failed to load Mill Name.");
               }
           }
