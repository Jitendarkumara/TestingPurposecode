@model List<TSMKhopoliTubeMillDashboard.Models.MachineSection>

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
        }

        th, td {
            border: 1px solid #000;
            padding: 1px 3px; /* Minimal padding */
            text-align: center;
            font-size: 11px; /* Compact font size */
            height: 18px; /* Set exact height similar to Excel */
            line-height: 18px; /* Prevents extra spacing */
            white-space: nowrap; /* Ensures no text wrapping */
        }

        th {
            background-color: #f2f2f2;
            font-size: 11px;
        }

        /* Green and Red Circles */
        .green-circle, .red-circle {
            font-size: 10px;
            vertical-align: middle; /* Aligns circles properly */
        }
    </style>
</head>
<body>

    <div class="container mt-4">
        <h4 class="text-center">Machine Data Table</h4>

        <div class="table-container">
            @foreach (var section in Model)
            {
                <h5>@section.SectionName</h5>
                <table>
                    <thead>
                        <tr>
                            <th>Parameter</th>
                            @foreach (var machine in section.Machines)
                            {
                                <th>@machine</th>
                            }
                        </tr>
                    </thead>
                    <tbody>
                        @foreach (var param in section.Parameters)
                        {
                            <tr>
                                <td><strong>@param.ParameterName</strong></td>
                                @foreach (var value in param.Values)
                                {
                                    <td>
                                        <span class="@(value ? "green-circle" : "red-circle")">●</span>
                                    </td>
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