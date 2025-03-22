window.onload = function () {
    let sidebar = document.querySelector(".sidebar");
    let closeBtn = document.querySelector("#btn");
    let searchBtn = document.querySelector(".bx-search");

    if (closeBtn) {
        closeBtn.addEventListener("click", () => {
            sidebar.classList.toggle("open");
            menuBtnChange();
        });
    } else {
        console.warn("Sidebar toggle button (#btn) not found.");
    }

    if (searchBtn) {
        searchBtn.addEventListener("click", () => {
            sidebar.classList.toggle("open");
            menuBtnChange();
        });
    } else {
        console.warn("Search button (.bx-search) not found.");
    }

    function menuBtnChange() {
        if (sidebar && closeBtn) {
            if (sidebar.classList.contains("open")) {
                closeBtn.classList.replace("bx-menu", "bx-menu-alt-right");
            } else {
                closeBtn.classList.replace("bx-menu-alt-right", "bx-menu");
            }
        }
    }

    // Restore button states from localStorage
    const hiddenButton = localStorage.getItem("hiddenButton");
    const visibleButton = localStorage.getItem("visibleButton");

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
};

function toggleButtons(clickedId, otherId, redirectUrl) {
    localStorage.setItem("hiddenButton", clickedId);
    localStorage.setItem("visibleButton", otherId);
    window.location.href = redirectUrl;
}