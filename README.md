<script>
    document.addEventListener("DOMContentLoaded", function () {
        const hiddenButton = localStorage.getItem("hiddenButton");
        const visibleButton = localStorage.getItem("visibleButton");

        if (hiddenButton && visibleButton) {
            const hiddenBtnElement = document.getElementById(hiddenButton);
            const visibleBtnElement = document.getElementById(visibleButton);

            if (hiddenBtnElement) hiddenBtnElement.style.display = "none";
            if (visibleBtnElement) visibleBtnElement.style.display = "inline-block";
        }
    });

    function toggleButtons(clickedId, otherId, redirectUrl) {
        // Hide clicked button and store the state
        localStorage.setItem("hiddenButton", clickedId);
        localStorage.setItem("visibleButton", otherId);

        // Navigate to the target page
        window.location.href = redirectUrl;
    }
</script>