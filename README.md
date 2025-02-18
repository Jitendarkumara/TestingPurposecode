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
        .green-circle { color: green; font-size: 2.5rem; }
        .red-circle { color: red; font-size: 2.5rem; }
    </style>
</head>
<body>
    <div class="container mt-4">
        <h5 class="text-center">Machine Data Table</h5>

        @foreach (var section1 in Model)
        {
            <h5 style="color:black">@section1.SectionName</h5>
            <table>
                <thead >
                    <tr>
                        <th style="color:black">Parameter</th>
                        @foreach (var machine in section1.Machines)
                        {
                            <th style="color:black">@machine</th>
                        }
                    </tr>
                </thead>
                <tbody>
                    @foreach (var param in section1.Parameters)
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
