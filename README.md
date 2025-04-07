function renderAllCharts() {
    document.getElementById("charts-container").innerHTML = "";

    mills.forEach(mill => {
        let chartDiv = document.createElement("div");
        chartDiv.classList.add("chart-wrapper");
        chartDiv.innerHTML = `
            <div class="chart-title">${mill}</div>
            <button class="Feeders-btn" data-mill="${mill}">Feeders</button>
            <canvas id="chart-${mill}"></canvas>
        `;
        document.getElementById("charts-container").appendChild(chartDiv);
        loadChartData(mill);
    });

    // Attach event listeners AFTER buttons are in DOM
    document.querySelectorAll(".Feeders-btn").forEach(btn => {
        btn.addEventListener("click", function () {
            const millName = this.getAttribute("data-mill");
            const fromDate = document.getElementById("FromDate").value;
            const toDate = document.getElementById("ToDate").value;
            const shift = document.getElementById("shiftSelect").value;
            const status = document.getElementById("statusSelect").value;

            const query = `mill=${encodeURIComponent(millName)}&from=${fromDate}&to=${toDate}&shift=${shift}&status=${status}`;
            window.open(`/FeedersChart.html?${query}`, '_blank');
        });
    });
}