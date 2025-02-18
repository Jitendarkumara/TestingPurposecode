@model List<YourNamespace.Models.MachineSection>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Machine Data Table</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
    <style>
        .green-circle { color: green; font-size: 1.5rem; }
        .red-circle { color: red; font-size: 1.5rem; }
    </style>
</head>
<body>
    <div class="container mt-4">
        <h2 class="text-center">Machine Data Table</h2>

        @foreach (var section in Model)
        {
            <h3 class="mt-4">@section.SectionName</h3>
            <table class="table table-bordered text-center">
                <thead class="table-dark">
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
</body>
</html>