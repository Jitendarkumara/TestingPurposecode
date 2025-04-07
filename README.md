Perfect! You can easily add a button to the top-left corner of each chart container. Let's call it something like “Details” or “Submills”, and position it using CSS.

Here's how to do it:

1. Update your chart HTML in renderAllCharts()

We'll add a button inside the chart wrapper aligned to the top-left.

2. Style the button

We'll make it visually distinct and position it top-left using position: absolute.

3. Handle button clicks

You can attach an event listener using jQuery or plain JavaScript to open submill data.


---

Updated renderAllCharts() function:

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


---

Add this to your <style> section:

.submill-btn {
    position: absolute;
    top: 10px;
    left: 10px;
    z-index: 10;
    padding: 5px 10px;
    background-color: darkorange;
    color: white;
    border: none;
    cursor: pointer;
    border-radius: 5px;
    font-size: 12px;
}


---

Result:

You’ll now have a “Submills” button at the top-left of each chart card. When clicked, you can show a modal, navigate to a new page, or load sub-chart data dynamically.

Need help showing a modal with submill charts or loading them on the same page?

