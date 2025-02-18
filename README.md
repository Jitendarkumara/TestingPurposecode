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

public IActionResult EmsDashboard()
{
    var sections = new List<MachineSection>
    {
        new MachineSection
        {
            SectionName = "Section1",
            Machines = new List<string> { "LDP Mill1", "LDP Mill2", "LDP Finishing 1", "LDP Finishing 2", "LDP External Coating", "LDP Internal Coating" },
            Parameters = new List<MachineParameter>
            {
                new MachineParameter { ParameterName = "Mill Run/Stop", Values = new List<bool> { true, true, true, true, true, true } },
                new MachineParameter { ParameterName = "Speed", Values = new List<bool> { false, false, false, false, false, false } },
                new MachineParameter { ParameterName = "Size", Values = new List<bool> { true, true, true, true, true, true } },
                new MachineParameter { ParameterName = "Thickness", Values = new List<bool> { true, true, true, true, true, true } },
                new MachineParameter { ParameterName = "Grade", Values = new List<bool> { false, false, false, false, false, false } },
                new MachineParameter { ParameterName = "Customer Name", Values = new List<bool> { false, true, true, true, false, false } },
                new MachineParameter { ParameterName = "Alert", Values = new List<bool> { false, true, true, true, false, false } },
                new MachineParameter { ParameterName = "KW", Values = new List<bool> { false, true, true, true, false, false } },
                new MachineParameter { ParameterName = "KWh", Values = new List<bool> { false, true, true, true, false, false } },
                new MachineParameter { ParameterName = "KVAh", Values = new List<bool> { false, true, true, true, false, false } }
            }
        },
        new MachineSection
        {
            SectionName = "Section2",
            Machines = new List<string> { "Tube", "GP-1", "GP-2", "CCL", "NCRM", "Skin Pass" },
            Parameters = new List<MachineParameter>
            {
                new MachineParameter { ParameterName = "Mill Run/Stop", Values = new List<bool> { true, true, true, true, true, true } },
                new MachineParameter { ParameterName = "Speed", Values = new List<bool> { false, false, false, false, false, false } },
                new MachineParameter { ParameterName = "Size", Values = new List<bool> { true, true, true, true, true, true } },
                new MachineParameter { ParameterName = "Thickness", Values = new List<bool> { true, true, true, true, true, true } },
                new MachineParameter { ParameterName = "Grade", Values = new List<bool> { false, false, false, false, false, false } },
                new MachineParameter { ParameterName = "Customer Name", Values = new List<bool> { false, true, true, true, false, false } },
                new MachineParameter { ParameterName = "Alert", Values = new List<bool> { false, true, true, true, false, false } },
                new MachineParameter { ParameterName = "KW", Values = new List<bool> { false, true, true, true, false, false } },
                new MachineParameter { ParameterName = "KWh", Values = new List<bool> { false, true, true, true, false, false } },
                new MachineParameter { ParameterName = "KVAh", Values = new List<bool> { false, true, true, true, false, false } }
            }
        },
        new MachineSection
        {
            SectionName = "Section3",
            Machines = new List<string> { "WCRM Mill-1", "WCRM Mill-2", "GP-2", "Utility", "Power Plant", "Service Center" },
            Parameters = new List<MachineParameter>
            {
                new MachineParameter { ParameterName = "Mill Run/Stop", Values = new List<bool> { true, true, true, true, true, true } },
                new MachineParameter { ParameterName = "Speed", Values = new List<bool> { false, false, false, false, false, false } },
                new MachineParameter { ParameterName = "Size", Values = new List<bool> { true, true, true, true, true, true } },
                new MachineParameter { ParameterName = "Thickness", Values = new List<bool> { true, true, true, true, true, true } },
                new MachineParameter { ParameterName = "Grade", Values = new List<bool> { false, false, false, false, false, false } },
                new MachineParameter { ParameterName = "Customer Name", Values = new List<bool> { false, true, true, true, false, false } },
                new MachineParameter { ParameterName = "Alert", Values = new List<bool> { false, true, true, true, false, false } },
                new MachineParameter { ParameterName = "KW", Values = new List<bool> { false, true, true, true, false, false } },
                new MachineParameter { ParameterName = "KWh", Values = new List<bool> { false, true, true, true, false, false } },
                new MachineParameter { ParameterName = "KVAh", Values = new List<bool> { false, true, true, true, false, false } }
            }
        }
    };
    return View(sections);
}


    public class MachineSection
    {
        public string SectionName { get; set; } // Example: "LDP Mill", "Tube Section", etc.
        public List<string> Machines { get; set; } // List of machine names as columns
        public List<MachineParameter> Parameters { get; set; } // List of parameters with TRUE/FALSE values
    }

    public class MachineParameter
    {
        public string ParameterName { get; set; } // Example: Mill Run/Stop, Speed, Size, etc.
        public List<bool> Values { get; set; } // Corresponding values for each machine in the section
    }
