<div class="col-md-2">
    <label for="dateFilter" class="fw-bold">Select Date</label>
    <input type="date" id="dateFilter" class="form-control m-0">
</div>

<script>
    document.addEventListener("DOMContentLoaded", function () {
        let today = new Date().toISOString().split('T')[0]; // Get today's date in YYYY-MM-DD format
        document.getElementById("dateFilter").value = today; // Set the default value
    });
</script>