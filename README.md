$(document).ready(function () {
    const today = new Date().toISOString().split("T")[0];
    $("#FromDate").val(today);
    $("#ToDate").val(today);
    
    renderAllCharts();
});