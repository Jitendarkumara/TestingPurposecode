 function loadMillname() {
     try {
         let url = "/Home/GetAllMill"; // API endpoint

         const response = await fetch(url);
         const data = await response.json();

         console.log("Fetched Data:", data);

         if (!data || data.length === 0) {
             alert("No MillName available!");
             return;
         }
         else
         {
             var Milloption=document.getElementById("millSelect");
             @foreach (var mill in data)
 {
     Milloption.add(option.value)
         < option value = "@mill.Millname" > @mill.AllMillName </ option >
 }

         }
     }
     catch (error) {
         console.error("Error loading MillName:", error);
         alert("Failed to load MillName.");
     }
     }
