<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"] EmsApplication</title>
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css" />
    <link rel="stylesheet" href="~/css/site.css" asp-append-version="true" />
    <link rel="stylesheet" href="~/EmsApplication.styles.css" asp-append-version="true" />
    <link rel="stylesheet" href="style.css">
    <link href='https://unpkg.com/boxicons@2.0.7/css/boxicons.min.css' rel='stylesheet'>
    <link href="~/css/sidebar.css" rel="stylesheet" />
    <style>
        .Saurabh {
            background-color: black;
            color: white;
            padding: 15px 20px;
            text-align: center;
            font-size: 24px;
            font-weight: bold;
            border-radius: 5px;
            box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
        }

        .ems-logo {
            height: 6%;
            width: 6%;
            margin-right: 10px;
        }
    </style>
</head>
<body>
    <div class="sidebar">
        <div class="logo-details logo">
            <img src="~/images/273000.svg" class="icon" alt="EMS Logo">
            <div class="logo_name"><strong>EMS Khopoli</strong></div>
            <i class='bx bx-menu' id="btn"></i>
        </div>

        <ul class="nav-list">
            <li>
                <a href="/Home/Index">
                    <i class='bx bx-home'></i>
                    <span class="links_name">Home</span>
                </a>
                <span class="tooltip">Home</span>
            </li>
            <li>
                <a href="#" onclick="location.href='/Home/DashBoard'">
                    <i class='bx bx-grid-alt'></i>
                    <span class="links_name">Dashboard</span>
                </a>
                <span class="tooltip">Dashboard</span>
            </li>
        </ul>
    </div>

    <section class="home-section">
        <div class="container" style="max-width: 1530px !important">
            <div class="container-fluid Saurabh">
                <div class="row">
                    <div class="col-12">
                        <h3>
                            <img src="~/images/smart-meter.png" alt="EMS Logo" class="ems-logo">
                            ENERGY MANAGEMENT SYSTEM
                            <img style="height:65px;margin-top: -8px;" src="~/images/giphyfinal.webp" />
                        </h3>
                    </div>
                </div>
            </div>
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
            @RenderBody()
        </div>
    </section>

    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
    <script src="~/js/site.js" asp-append-version="true"></script>
    @await RenderSectionAsync("Scripts", required: false)

    <script>
        document.addEventListener("DOMContentLoaded", function () {
            let sidebar = document.querySelector(".sidebar");
            let closeBtn = document.querySelector("#btn");
            let searchBtn = document.querySelector(".bx-search");

            // Ensure elements exist before adding event listeners
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
        });

        function toggleButtons(clickedId, otherId, redirectUrl) {
            localStorage.setItem("hiddenButton", clickedId);
            localStorage.setItem("visibleButton", otherId);
            window.location.href = redirectUrl;
        }
    </script>
</body>
</html>