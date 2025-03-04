public IActionResult Index()
{
    try
    {
        // Creating an instance of MachineSectionDal to fetch machine running data
        MachineSectionDal M = new MachineSectionDal();
        DataTable dt = new DataTable();
        dt = M.getEmsMachineRunningData(); // Fetch data

        // Ensure DataTable has enough rows before accessing specific indexes
        if (dt.Rows.Count < 5)
        {
            return BadRequest("Insufficient data in DataTable.");
        }

        // Extracting machine running status values
        double mill1 = Convert.ToDouble(dt.Rows[4]["Running_Status"]),
               mill2 = Convert.ToDouble(dt.Rows[2]["Running_Status"]),
               finishing1 = Convert.ToDouble(dt.Rows[3]["Running_Status"]),
               finishing2 = Convert.ToDouble(dt.Rows[1]["Running_Status"]),
               extcoating = Convert.ToDouble(dt.Rows[0]["Running_Status"]);

        // Extracting alert values
        double sumMill1 = Convert.ToDouble(dt.Rows[4]["Alert"]),
               sumMill2 = Convert.ToDouble(dt.Rows[2]["Alert"]),
               sumFinishing1 = Convert.ToDouble(dt.Rows[3]["Alert"]),
               sumFinishing2 = Convert.ToDouble(dt.Rows[1]["Alert"]),
               sumExtcoating = Convert.ToDouble(dt.Rows[0]["Alert"]);

        // Creating machine sections
        var sections = new List<MachineSection>
        {
            new MachineSection
            {
                SectionName = "Section1",
                Machines = new List<string> 
                { 
                    "LDP Mill1", "LDP Mill2", "LDP Finishing 1", "LDP Finishing 2", 
                    "LDP External Coating", "LDP Internal Coating", "Tube", "GP-1", "GP-2" 
                },
                Parameters = new List<MachineParameter>
                {
                    new MachineParameter { ParameterName = "Mill Run/Stop", Values = new List<double> { mill1, mill2, finishing1, finishing2, extcoating, 2, 2, 2, 2 } },
                    new MachineParameter { ParameterName = "Speed", Values = new List<double> { 4, 5, 4, 8, 2, 3, 6, 9, 2 } },
                    new MachineParameter { ParameterName = "Size", Values = new List<double> { 2, 5, 7, 8, 9, 6, 3, 7, 5 } },
                    new MachineParameter { ParameterName = "Thickness", Values = new List<double> { 4, 4, 4, 4, 4, 4, 2, 4, 2 } },
                    new MachineParameter { ParameterName = "Grade", Values = new List<double> { 2, 2, 2, 2, 2, 2, 2, 4, 2 } },
                    new MachineParameter { ParameterName = "Customer Name", Values = new List<double> { 2, 4, 4, 4, 2, 2, 2, 4, 2 } },
                    new MachineParameter { ParameterName = "Alert", Values = new List<double> { sumMill1, sumMill2, sumFinishing1, sumFinishing2, sumExtcoating, 2, 2, 2, 2 } },
                    new MachineParameter { ParameterName = "KW", Values = new List<double> { 2, 4, 4, 4, 2, 2, 2, 4, 2 } },
                    new MachineParameter 
                    { 
                        ParameterName = "KWh", 
                        Values = new List<double> 
                        { 
                            Convert.ToDouble(dt.Rows[4]["Act_value"]), 
                            Convert.ToDouble(dt.Rows[2]["Act_value"]), 
                            Convert.ToDouble(dt.Rows[3]["Act_value"]), 
                            Convert.ToDouble(dt.Rows[1]["Act_value"]), 
                            Convert.ToDouble(dt.Rows[0]["Act_value"]), 
                            2, 2, 2, 2 
                        } 
                    },
                    new MachineParameter { ParameterName = "KVAh", Values = new List<double> { 2, 4, 4, 4, 2, 2, 2, 4, 2 } }
                }
            },
            new MachineSection
            {
                SectionName = "Section2",
                Machines = new List<string> 
                { 
                    "CCL", "NCRM", "Skin Pass", "WCRM Mill-1", "WCRM Mill-2", "GP-2", "Utility", "Power Plant", "Service Center"  
                },
                Parameters = new List<MachineParameter>
                {
                    new MachineParameter { ParameterName = "Mill Run/Stop", Values = new List<double> { 4, 4, 4, 4, 4, 4, 2, 4, 2 } },
                    new MachineParameter { ParameterName = "Speed", Values = new List<double> { 2, 2, 2, 2, 2, 2, 2, 4, 2 } },
                    new MachineParameter { ParameterName = "Size", Values = new List<double> { 4, 4, 4, 4, 4, 4, 2, 4, 2 } },
                    new MachineParameter { ParameterName = "Thickness", Values = new List<double> { 4, 4, 4, 4, 4, 4, 2, 4, 2 } },
                    new MachineParameter { ParameterName = "Grade", Values = new List<double> { 2, 2, 2, 2, 2, 2, 2, 4, 2 } },
                    new MachineParameter { ParameterName = "Customer Name", Values = new List<double> { 2, 4, 4, 4, 2, 2, 2, 4, 2 } },
                    new MachineParameter { ParameterName = "Alert", Values = new List<double> { 2, 4, 4, 4, 2, 2, 2, 4, 2 } },
                    new MachineParameter { ParameterName = "KW", Values = new List<double> { 2, 4, 4, 4, 2, 2, 2, 4, 2 } },
                    new MachineParameter { ParameterName = "KWh", Values = new List<double> { 2, 2, 2, 2, 2, 2, 2, 2, 2 } },
                    new MachineParameter { ParameterName = "KVAh", Values = new List<double> { 2, 4, 4, 4, 2, 2, 2, 4, 2 } }
                }
            }
        };

        // Returning the machine sections data to the view
        return View(sections);
    }
    catch (Exception ex)
    {
        // Logging the error for debugging
        Console.WriteLine($"Error in Index method: {ex.Message}");
        
        // Return an error response
        return StatusCode(500, "An error occurred while processing the request.");
    }
}