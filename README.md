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
    <!-- Boxicons CDN Link -->
    <link href='https://unpkg.com/boxicons@2.0.7/css/boxicons.min.css' rel='stylesheet'>
    <link href="~/css/sidebar.css" rel="stylesheet" />
    <style>
        #refresh-indicator {
            position: fixed;
            top: 80px;
            right: 10px;
            padding: 8px 12px;
          /*   background: #f0f0f0; */
          color:red;
            border-radius: 4px;
            font-size: 12px;
            transition: background-color 0.3s ease;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
            z-index: 1000;
        }
        .sidebar .logo {
            display: flex;
            align-items: center;
            padding: 10px;
        }

        .sidebar .logo img {
                height: 40px;
                margin-right: 10px;
         }

        .custom-btn {
            background: linear-gradient(45deg, #007bff, #0056b3);
            color: white;
            padding: 12px ;
            font-size: 16px;
            border: none;
            border-radius: 8px;
            transition: 0.3s ease-in-out;
            box-shadow: 2px 4px 10px rgba(0, 0, 0, 0.2);
        }

            .custom-btn:hover {
                background: linear-gradient(45deg, #0056b3, #004085);
                transform: scale(1.05);
            }

        .modal-content {
            border-radius: 15px;
            box-shadow: 0px 5px 15px rgba(0, 0, 0, 0.2);
        }

        .modal-title {
            font-weight: bold;
            color: #007bff;
            text-align: center;
            width: 100%;
        }

        .form-control, .form-select {
            border-radius: 8px;
        }

        .btn-primary {
            background-color: #007bff;
            border: none;
            border-radius: 8px;
            padding: 10px 15px;
            font-size: 16px;
        }

            .btn-primary:hover {
                background-color: #0056b3;
            }

        .btn-secondary {
            border-radius: 8px;
            padding: 10px 15px;
        }

        /*  4CBB17    #11101D */
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

        body {
            font-family: Arial, sans-serif;
            text-align: center;
        }
        ol,
        ul {
            padding-left: 0rem !important;
        }
        .ems-logo{
            height:6%;
            width:6%;
            margin-right:10px;
        }
    </style>
</head>
<body>
    @* <header>
        <div>
            <h4 class="text-center" style="background-color:cornflowerblue">EMS DashBoard</h4>
        </div>
        
    </header> *@
    <div class="sidebar">
        <div class="logo-details logo">
            <img src="~/images/273000.svg" class="icon" alt="EMS Logo">
            <div class="logo_name">
                <strong>EMS Khopoli</strong>
            </div>
            <i class='bx bx-menu' id="btn"></i>
        </div>

        <ul class="nav-list">
            @* <li>
                <i class='bx bx-search'></i>
                <input type="text" placeholder="Search...">
                <span class="tooltip">Search</span>
            </li> *@
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

            <li>
                <a href="#">
                    <i class='bx bx-pie-chart-alt-2'></i>
                    <span class="links_name">Analytics</span>
                </a>
                <span class="tooltip">Analytics</span>
            </li>
            
            
        </ul>
    </div>
    <section class="home-section">
        <div class="container" style="max-width: 1530px !important">
            <div class="container-fluid Saurabh">
                <div class="row">
                    <div class="col-12">
                        <div class="header " style="font-family:sans-serif;height:43px;">
                            <h3 class="gradient-text">
                                <img src="~/images/smart-meter.png" alt="EMS Logo" class="ems-logo">
                                ENERGY MANAGEMENT SYSTEM
                                <img style="height:65px;margin-top: -8px;" src="~/images/giphyfinal.webp" />
                           </h3> 

                        </div>
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
               @*  <div class="col-2">
                    <button id="exportExcel" type="button" class="custom-btn m-0">Export to Excel</button> <!-- Excel Export Button onclick="ChartDataLoad()" -->
                </div> *@
                <div class="col-2">
                    <button type="button" class="custom-btn m-0" data-bs-toggle="modal" data-bs-target="#machineParameterModal">
                        Input Parameter
                    </button>
                </div>
                
               

            </div>
            @RenderBody()
        </div>
         
    </section>

    @* <footer class="border-top footer text-muted">
        <div class="container">
            &copy; 2025 - EmsApplication - <a asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
        </div>
    </footer> *@
    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
    <script src="~/js/site.js" asp-append-version="true"></script>
    @await RenderSectionAsync("Scripts", required: false)
    <script>
        let sidebar = document.querySelector(".sidebar");
        let closeBtn = document.querySelector("#btn");
        let searchBtn = document.querySelector(".bx-search");

        closeBtn.addEventListener("click", ()=>{
          sidebar.classList.toggle("open");
          menuBtnChange();//calling the function(optional)
        });

        searchBtn.addEventListener("click", ()=>{ // Sidebar open when you click on the search iocn
          sidebar.classList.toggle("open");
          menuBtnChange(); //calling the function(optional)
        });

        // following are the code to change sidebar button(optional)
        function menuBtnChange() {
         if(sidebar.classList.contains("open")){
           closeBtn.classList.replace("bx-menu", "bx-menu-alt-right");//replacing the iocns class
         }else {
           closeBtn.classList.replace("bx-menu-alt-right","bx-menu");//replacing the iocns class
         }
        }
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
</body>
</html>
