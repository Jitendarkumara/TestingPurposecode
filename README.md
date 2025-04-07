document.querySelectorAll(".submill-btn").forEach(btn => {
    btn.addEventListener("click", function () {
        const millName = this.getAttribute("data-mill");
        const fromDate = document.getElementById("FromDate").value;
        const toDate = document.getElementById("ToDate").value;
        const shift = document.getElementById("shiftSelect").value;
        const status = document.getElementById("statusSelect").value;

        const query = `mill=${encodeURIComponent(millName)}&from=${fromDate}&to=${toDate}&shift=${shift}&status=${status}`;
        window.open(`/SubmillChart.html?${query}`, '_blank');
    });
});