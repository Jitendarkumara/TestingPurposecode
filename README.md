<script>
    document.addEventListener("DOMContentLoaded", function () {
        // Retrieve stored button states
        const hiddenButton = localStorage.getItem("hiddenButton");
        const visibleButton = localStorage.getItem("visibleButton");

        console.log("Hidden Button:", hiddenButton);
        console.log("Visible Button:", visibleButton);

        if (hiddenButton && visibleButton) {
            const hiddenBtnElement = document.getElementById(hiddenButton);
            const visibleBtnElement = document.getElementById(visibleButton);

            if (hiddenBtnElement) {
                hiddenBtnElement.style.display = "none";
            } else {
                console.warn(`Button with ID '${hiddenButton}' not found.`);
            }

            if (visibleBtnElement) {
                visibleBtnElement.style.display = "inline-block";
            } else {
                console.warn(`Button with ID '${visibleButton}' not found.`);
            }
        }
    });

    function toggleButtons(clickedId, otherId, redirectUrl) {
        console.log(`Hiding: ${clickedId}, Showing: ${otherId}, Redirecting to: ${redirectUrl}`);

        // Store button state
        localStorage.setItem("hiddenButton", clickedId);
        localStorage.setItem("visibleButton", otherId);

        // Redirect to the target page
        window.location.href = redirectUrl;
    }
</script>