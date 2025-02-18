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
            padding: 5px;
            text-align: center;
            font-size: 14px;
        }

        th {
            background-color: #f2f2f2;
        }

        /* Green and Red Circles */
        .green-circle { color: green; font-size: 1.2rem; }
        .red-circle { color: red; font-size: 1.2rem; }
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