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
               }

           tr td {
                   border: 1px solid #000;
                   padding: -50px;
                   text-align: center;
                   font-size: 14px;
                   height:5px;
               }
               tr{
                   height:12px;
               }

               th {
                   background-color: #f2f2f2;
               }

        /*        Green and Red Circles */
               .green-circle { color: green; font-size: 16px; }
               .red-circle { color: red; font-size: 16px; }

       
    </style>
</head>
<body>

    <div>
        <h4 class="text-center">Machine Data Table</h4>

        <div>
            @foreach (var section1 in Model)
            {
                <h5>@section1.SectionName</h5>
                <table>
                    <thead>
                        <tr >
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
        public IActionResult Index()
        {
            var sections = new List<MachineSection>
            {
                new MachineSection
                {
                    SectionName = "Section1",
                    Machines = new List<string> { "LDP Mill1", "LDP Mill2", "LDP Finishing 1", "LDP Finishing 2", "LDP External Coating", "LDP Internal Coating","Tube", "GP-1", "GP-2" },
                    Parameters = new List<MachineParameter>
                    {
                        new MachineParameter { ParameterName = "Mill Run/Stop", Values = new List<double> { 1, 1, 1, 1, 1, 1,0, 1, 0 } },
                        new MachineParameter { ParameterName = "Speed", Values = new List<double> {1,5,4,8,2,3,6,9,2 } },
                        new MachineParameter { ParameterName = "Size", Values = new List<double> {  } },
                        new MachineParameter { ParameterName = "Thickness", Values = new List<double> { 1, 1, 1, 1, 1, 1, 0, 1, 0 } },
                        new MachineParameter { ParameterName = "Grade", Values = new List<double> { 0, 0, 0, 0, 0, 0, 0, 1, 0 } },
                        new MachineParameter { ParameterName = "Customer Name", Values = new List<double> { 0, 1, 1, 1, 0, 0, 0, 1, 0 } },
                        new MachineParameter { ParameterName = "Alert", Values = new List<double> { 0, 1, 1, 1, 0, 0, 0, 1, 0 } },
                        new MachineParameter { ParameterName = "KW", Values = new List<double> { 0, 1, 1, 1, 0, 0, 0, 1, 0 } },
                        new MachineParameter { ParameterName = "KWh", Values = new List<double> { 0, 1, 1, 1, 0, 0, 0, 1, 0 } },
                        new MachineParameter { ParameterName = "KVAh", Values = new List<double> { 0, 1, 1, 1, 0, 0, 0, 1, 0 } }
                    }
                },
                new MachineSection
                {
                    SectionName = "Section2",
                    Machines = new List<string> {  "CCL", "NCRM", "Skin Pass","WCRM Mill-1", "WCRM Mill-2", "GP-2", "Utility", "Power Plant", "Service Center"  },
                    Parameters = new List<MachineParameter>
                    {
                        new MachineParameter { ParameterName = "Mill Run/Stop", Values = new List<double> { 1, 1, 1, 1, 1, 1, 0, 1, 0 } },
                        new MachineParameter { ParameterName = "Speed", Values = new List<double> { 0, 0, 0, 0, 0, 0, 0, 1, 0 } },
                        new MachineParameter { ParameterName = "Size", Values = new List<double> { 1, 1, 1, 1, 1, 1, 0, 1, 0 } },
                        new MachineParameter { ParameterName = "Thickness", Values = new List<double> { 1, 1, 1, 1, 1, 1, 0, 1, 0 } },
                        new MachineParameter { ParameterName = "Grade", Values = new List<double> { 0, 0, 0, 0, 0, 0, 0, 1, 0 } },
                        new MachineParameter { ParameterName = "Customer Name", Values = new List<double> { 0, 1, 1, 1, 0, 0, 0, 1, 0 } },
                        new MachineParameter { ParameterName = "Alert", Values = new List<double> { 0, 1, 1, 1, 0, 0, 0, 1, 0 } },
                        new MachineParameter { ParameterName = "KW", Values = new List<double> { 0, 1, 1, 1, 0, 0, 0, 1, 0 } },
                        new MachineParameter { ParameterName = "KWh", Values = new List<double> { 0, 1, 1, 1, 0, 0, 0, 1, 0 } },
                        new MachineParameter { ParameterName = "KVAh", Values = new List<double> { 0, 1, 1, 1, 0, 0, 0, 1, 0 } }
                    }
                },
               
            };
            return View(sections);
        }
