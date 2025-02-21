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
            table-layout: fixed;
        }

        tr td, th {
            border: 1px solid #000;
            padding: 5px;
            text-align: center;
            font-size: 14px;
            width: 10%;
            word-wrap: break-word;
        }

        th {
            background-color: #f2f2f2;
        }

        /* Green and Red Circles */
        .green-circle { color: green; font-size: 25px; }
        .red-circle { color: red; font-size: 25px; }
    </style>

    <!-- jQuery and SheetJS (xlsx) for Excel export -->
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.17.4/xlsx.full.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/FileSaver.js/2.0.5/FileSaver.min.js"></script>

    <script>
        function ChartDataLoad() {
            window.location.href = "/Home/DashBoard";
        }

        $(document).ready(function () {
            $("#exportExcel").click(function () {
                var wb = XLSX.utils.book_new(); // Create a new workbook

                $("table").each(function (index, table) {
                    var ws = XLSX.utils.table_to_sheet(table); // Convert table to sheet
                    XLSX.utils.book_append_sheet(wb, ws, "Section_" + (index + 1)); // Append sheet to workbook
                });

                XLSX.writeFile(wb, "MachineData.xlsx"); // Save the file
            });
        });
    </script>
</head>
<body>

    <div>
        <button onclick="ChartDataLoad()">ChartDashboard</button>
        <button id="exportExcel">Export to Excel</button> <!-- Excel Export Button -->
    </div>

    <div>
        @foreach (var section1 in Model)
        {
            <h5>@section1.SectionName</h5>
            <table>
                <thead>
                    <tr>
                        <th>Parameter</th>
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
                            <td><strong>@param.ParameterName</strong></td>

                            @if (param.Values.Count > 0) // Prevent empty list error
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
                                        }
                                        else
                                        {
                                            @value <!-- Display numeric values for other parameters -->
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

</body>
</html>