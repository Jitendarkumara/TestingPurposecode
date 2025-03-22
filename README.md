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
    // Store state in localStorage before redirecting
    localStorage.setItem("hiddenButton", clickedId);
    localStorage.setItem("visibleButton", otherId);

    // Redirect to the target page
    window.location.href = redirectUrl;
}

// Apply stored button visibility on page load
document.addEventListener("DOMContentLoaded", function () {
    const hiddenButton = localStorage.getItem("hiddenButton");
    const visibleButton = localStorage.getItem("visibleButton");

    if (hiddenButton && visibleButton) {
        // Hide the stored hidden button
        document.getElementById(hiddenButton).style.display = "none";

        // Show the stored visible button
        document.getElementById(visibleButton).style.display = "inline-block";
    }
});
</script>