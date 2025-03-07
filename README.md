
               async function loadChartData(selectedMill="Mill1",selectedFeeder="All") {
            try {
                let url = "/Home/GetKWHData"; // Base API endpoint

                // Append the selected mill as a query parameter
                if (selectedMill) {
                    url += `?millName=${encodeURIComponent(selectedMill)}`;
                }
