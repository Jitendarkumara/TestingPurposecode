function renderAllCharts() {
    document.getElementById("charts-container").innerHTML = "";
    mills.forEach(mill => {
        let chartDiv = document.createElement("div");
        chartDiv.classList.add("chart-wrapper");
        chartDiv.innerHTML = `
            <div class="chart-title">${mill}</div>
            <button class="submill-btn" data-mill="${mill}">Submills</button>
            <canvas id="chart-${mill}"></canvas>
        `;
        document.getElementById("charts-container").appendChild(chartDiv);
        loadChartData(mill);
    });

    // Handle submill button click
    document.querySelectorAll(".submill-btn").forEach(btn => {
        btn.addEventListener("click", function () {
            const millName = this.getAttribute("data-mill");
            alert("Open Submill data for: " + millName);
            // You can replace alert with a modal, navigation, or AJAX call to load submill data
        });
    });
}