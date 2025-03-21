window.onload = function () {
    // ✅ Set default date to today
    let today = new Date().toISOString().split('T')[0]; 
    let dateInput = document.getElementById("dateFilter");

    if (dateInput) {
        dateInput.value = today; // Set today's date
    } else {
        console.warn("Date filter input not found!");
    }

    // ✅ Load other data
    loadChartData();
    loadMillname();
};