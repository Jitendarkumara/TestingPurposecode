@model List<YourNamespace.Models.MachineData>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Machine Data Table</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
    <style>
        .status-icon {
            width: 20px;
            height: 20px;
            border-radius: 50%;
            display: inline-block;
        }
        .green { background-color: green; }
        .red { background-color: red; }
    </style>
</head>
<body>
    <div class="container mt-4">
        <h2 class="text-center">Machine Data Table</h2>
        <table class="table table-bordered text-center">
            <thead>
                <tr>
                    <th>Parameter</th>
                    @foreach (var machine in Model)
                    {
                        <th>@machine.MachineName</th>
                    }
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>Mill Run/Stop</td>
                    @foreach (var machine in Model)
                    {
                        <td>
                            <span class="status-icon @(machine.IsRunning ? "green" : "red")"></span>
                            @(machine.IsRunning ? "Run" : "Stop")
                        </td>
                    }
                </tr>
                <tr>
                    <td>Speed</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.Speed</td>
                    }
                </tr>
                <tr>
                    <td>Size</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.Size</td>
                    }
                </tr>
                <tr>
                    <td>Thickness</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.Thickness</td>
                    }
                </tr>
                <tr>
                    <td>Grade</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.Grade</td>
                    }
                </tr>
                <tr>
                    <td>Customer Name</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.CustomerName</td>
                    }
                </tr>
                <tr>
                    <td>Alert</td>
                    @foreach (var machine in Model)
                    {
                        <td>
                            <span class="status-icon @(machine.Alert == "Low" ? "green" : "red")"></span>
                            @machine.Alert
                        </td>
                    }
                </tr>
                <tr>
                    <td>KW</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.KW</td>
                    }
                </tr>
                <tr>
                    <td>KWh</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.KWh</td>
                    }
                </tr>
                <tr>
                    <td>KVAh</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.KVAh</td>
                    }
                </tr>
            </tbody>
        </table>
    </div>
</body>
</html>