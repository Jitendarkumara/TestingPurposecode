@model List<EmsApplication.Models.MachineSection>

@{
    ViewData["Title"] = "EmsDashboard";
}

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Machine Data Table</title>

    <style>
        body {
            font-family: Arial, sans-serif;
        }

        .table-container {
            width: 100%;
            overflow-x: auto;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            border: 1px solid #000;
            table-layout:fixed;
        }

        tr td {
            border: 1px solid #000;
            padding: 5px;
            text-align: center;
            font-size: 14px;
            width:10%;
            word-wrap:break-word;
        }

        th {
            border: 1px solid #000;
            background-color: #f2f2f2;
        }

        /* Green and Red Circles */
        .green-circle { color: green; font-size: 25px; }
        .red-circle { color: red; font-size: 25px; }
    </style>
    <script>
        function ChartDataLoad()
        {
          window.location.href = "/Home/DashBoard"
        }
    </script>
</head>
<body>
    <div> <button onclick="ChartDataLoad()">ChartDashboard</button></div>
    <div>
   

        <div>
            @foreach (var section1 in Model)
            {
                <h5>@section1.SectionName</h5>
                <table>
                    <thead>
                        <tr>
                            <th >Parameter</th>
                            @foreach (var machine in section1.Machines)
                            {
                                <th>@machine</th>
                            }
                        </tr>
                    </thead>
                    <tbody>
                        @foreach (var param in section1.Parameters)
                        {
                            <tr>
                                <td ><strong>@param.ParameterName</strong></td>

                                @if (param.Values.Count>0) // Prevent empty list error
                                {
                                    @foreach (var value in param.Values)
                                    {
                                        <td>
                                            @if (param.ParameterName == "Mill Run/Stop" || param.ParameterName == "Alert")
                                            {
                                                <!-- Show circles only for Mill Run/Stop and Alert -->
                                                @if (value == 1)
                                                {
                                                    <span class="red-circle">●</span>
                                                }
                                                else if (value == 0)
                                                {
                                                    <span class="green-circle">●</span>
                                                }
                                                else
                                                {
                                                   
                                                }
                                            }
                                            else
                                            {
                                                <!-- Show actual values for other parameters -->
                                              @*   @value *@
                                            }
                                        </td>
                                    }
                                }
                                else
                                {
                                    <td colspan="@section1.Machines.Count">No Data</td>
                                }
                            </tr>
                        }
                    </tbody>
                </table>
            }
        </div>
    </div>

</body>
</html>
