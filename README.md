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
function toggleButtons(clickedId, otherId, redirectUrl) {
    // Hide the clicked button
    document.getElementById(clickedId).style.display = "none";
    
    // Show the other button
    document.getElementById(otherId).style.display = "inline-block";

    // Redirect to the target page
    window.location.href = redirectUrl;
}
</script>