 public IActionResult Index()
 {
     MachineSectionDal M = new MachineSectionDal();
     DataTable dt = new DataTable();
     dt = M.getEmsMachineRunningData();
     double mill1 = Convert.ToDouble(dt.Rows[4]["Running_Status"]),
         Mill2 = Convert.ToDouble(dt.Rows[2]["Running_Status"]),
         Finishing1 = Convert.ToDouble(dt.Rows[3]["Running_Status"]),
         Finishing2 = Convert.ToDouble(dt.Rows[1]["Running_Status"]),
         Extcoating = Convert.ToDouble(dt.Rows[0]["Running_Status"]);
     double SumMill1 = Convert.ToDouble(dt.Rows[4]["Alert"]),
         SumMill2 = Convert.ToDouble(dt.Rows[2]["Alert"]),
         SumFinishing1 = Convert.ToDouble(dt.Rows[3]["Alert"]),
         SumFinishing2 = Convert.ToDouble(dt.Rows[1]["Alert"]),
         SumExtcoating = Convert.ToDouble(dt.Rows[0]["Alert"]);
     //-----------------checking data here that fullfill criteria or not------------------------------------------------------------
    
   
     //-----------------------------------------------------------
     var sections = new List<MachineSection>
     {
         new MachineSection
         {
             SectionName = "Section1",
             Machines = new List<string> { "LDP Mill1", "LDP Mill2", "LDP Finishing 1", "LDP Finishing 2", "LDP External Coating", "LDP Internal Coating","Tube", "GP-1", "GP-2" },
             Parameters = new List<MachineParameter>
             {
               //  new MachineParameter { ParameterName = "Mill Run/Stop", Values = new List<double> { 1, 1, 1, 1, 1, 1,0, 1, 0 } },
                 new MachineParameter { ParameterName = "Mill Run/Stop", Values = new List<double> { mill1, Mill2,Finishing1,Finishing2,Extcoating, 2,2, 2, 2 } },


                 new MachineParameter { ParameterName = "Speed", Values = new List<double> {4,5,4,8,2,3,6,9,2 } },
                 new MachineParameter { ParameterName = "Size", Values = new List<double> { 2,5,7,8,9,6,3,7,5 } },
                 new MachineParameter { ParameterName = "Thickness", Values = new List<double> { 4, 4, 4, 4, 4, 4, 2, 4, 2 } },
                 new MachineParameter { ParameterName = "Grade", Values = new List<double> { 2, 2, 2, 2, 2, 2, 2, 4, 2 } },
                 new MachineParameter { ParameterName = "Customer Name", Values = new List<double> { 2, 4, 4, 4, 2, 2, 2, 4, 2 } },
                // new MachineParameter { ParameterName = "Alert", Values = new List<double> { 0, 1, 1, 1, 0, 0, 0, 1, 0 } },
                 new MachineParameter { ParameterName = "Alert", Values = new List<double> { SumMill1, SumMill2, SumFinishing1, SumFinishing2, SumExtcoating, 2,2, 2, 2 } },

                 new MachineParameter { ParameterName = "KW", Values = new List<double> { 2, 4, 4, 4, 2, 2, 2, 4, 2 } },

                    new MachineParameter { ParameterName = "KWh", Values = new List<double> { Convert.ToDouble(dt.Rows[4]["Act_value"]), Convert.ToDouble(dt.Rows[2]["Act_value"]), Convert.ToDouble(dt.Rows[3]["Act_value"]), Convert.ToDouble(dt.Rows[1]["Act_value"]), Convert.ToDouble(dt.Rows[0]["Act_value"]), 2, 2, 2 } },

                 new MachineParameter { ParameterName = "KVAh", Values = new List<double> { 2, 4, 4, 4, 2, 2, 2, 4, 2 } }
             }
         },
         new MachineSection
         {
             SectionName = "Section2",
             Machines = new List<string> {  "CCL", "NCRM", "Skin Pass","WCRM Mill-1", "WCRM Mill-2", "GP-2", "Utility", "Power Plant", "Service Center"  },
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
                    //new MachineParameter { ParameterName = "KWh", Values = new List<double> { SumMill1, SumMill2, SumFinishing1, SumFinishing2, SumExtcoating, 2,2, 2, 2 } },

                      new MachineParameter { ParameterName = "KWh", Values = new List<double> { 2, 2, 2, 2, 2, 2,2, 2, 2 } },

                 new MachineParameter { ParameterName = "KVAh", Values = new List<double> { 2, 4, 4, 4, 2, 2, 2, 4, 2 } }
             }
         },

     };



     return View(sections);
 }
