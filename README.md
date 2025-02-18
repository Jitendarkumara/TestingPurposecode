I understand that you want to create a table in ASP.NET Core where:

1. Machine names are columns


2. Parameters (Mill Run/Stop, Speed, Size, etc.) are row headers


3. Boolean values (TRUE/FALSE) are displayed in the cells


4. Multiple sections (LDP Mills, Tube/GP, WCRM Mills, etc.)




---

1. Update the Model

We need a model that represents this structured data dynamically.

Models/MachineData.cs

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


---

2. Update the Controller

Now, let's fill this model with sample data.

Controllers/HomeController.cs

using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using YourNamespace.Models;

namespace YourNamespace.Controllers
{
    public class HomeController : Controller
    {
        public IActionResult Index()
        {
            var sections = new List<MachineSection>
            {
                new MachineSection
                {
                    SectionName = "LDP Mills",
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
                    SectionName = "Tube & GP",
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
                    SectionName = "WCRM & Utility",
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
    }
}


---

3. Update the View

Now, let's loop through the sections dynamically to create the table.

Views/Home/Index.cshtml

@model List<YourNamespace.Models.MachineSection>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Machine Data Table</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
</head>
<body>
    <div class="container mt-4">
        <h2 class="text-center">Machine Data Table</h2>

        @foreach (var section in Model)
        {
            <h3 class="mt-4">@section.SectionName</h3>
            <table class="table table-bordered text-center">
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
                            <td>@param.ParameterName</td>
                            @foreach (var value in param.Values)
                            {
                                <td>@(value ? "✔️" : "❌")</td>
                            }
                        </tr>
                    }
                </tbody>
            </table>
        }
    </div>
</body>
</html>


---

Final Steps

1. Run the project (F5 in Visual Studio)


2. Your data will be displayed dynamically in tables as per your format.



Would you like me to help with styling, database connection, or real-time updates?

