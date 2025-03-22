<div class="row" style="margin-top:5px">
    <div class="col-2">
        <button id="dashboardBtn" type="button" class="custom-btn m-0" onclick="toggleButtons('dashboardBtn', 'homeBtn', '/Home/DashBoard')">
            Chart Dashboard
        </button>
    </div>
    <div class="col-2">
        <button id="homeBtn" type="button" class="custom-btn m-0" onclick="toggleButtons('homeBtn', 'dashboardBtn', '/Home/index')">
            Home
        </button>
    </div>
    <div class="col-2">
        <button type="button" class="custom-btn m-0" data-bs-toggle="modal" data-bs-target="#machineParameterModal">
            Input Parameter
        </button>
    </div>
</div>

<script>
// Function to toggle button visibility and store in localStorage
function toggleButtons(clickedId, otherId, redirectUrl) {
    // Hide clicked button
    document.getElementById(clickedId).style.display = "none";
    // Show the other button
    document.getElementById(otherId).style.display = "inline-block";

    // Store state in localStorage
    localStorage.setItem("hiddenButton", clickedId);

    // Redirect to target page
    window.location.href = redirectUrl;
}

// Apply stored button visibility on page load
document.addEventListener("DOMContentLoaded", function () {
    const hiddenButton = localStorage.getItem("hiddenButton");

    if (hiddenButton) {
        document.getElementById(hiddenButton).style.display = "none";

        // Find the other button and show it
        const otherButton = hiddenButton === "homeBtn" ? "dashboardBtn" : "homeBtn";
        document.getElementById(otherButton).style.display = "inline-block";
    }
});
</script>