 window.onload=function()
 {
    document.addEventListener("DOMContentLoaded", function () {
         let today = new Date().toISOString().split('T')[0]; // Get today's date in YYYY-MM-DD format
         document.getElementById("dateFilter").value = today; // Set the default value
     });
 loadChartData();
 loadMillname();
 // let selectedMill=document.getElementById("millSelect").value;
//  loadFeedername(selectedMill);
 }
