        public IActionResult Dashboard(string tableName = "TubeMill3",DateTime Tdate)
        {
            ViewBag.AvailableTables = new List<string>
    {
        "TubeMill1",
        "TubeMill2",
        "TubeMill3",
        "TubeMill4",
        "TubeMill7"
    };

            // Fetch TubeMill data dynamically based on selected table
            IEnumerable<dynamic> data = tableName switch
            {
                "TubeMill1" => _context.TubeMill_1.OrderByDescending(t => t.Timestamp).Take(5000).ToList(),
                "TubeMill2" => _context.TubeMill_2.OrderByDescending(t => t.Timestamp).Take(5000).ToList(),
                "TubeMill3" => _context.TubeMill_3.OrderByDescending(t => t.Timestamp).Take(5000).ToList(),
                "TubeMill4" => _context.TubeMill_4.OrderByDescending(t => t.Timestamp).Take(5000).ToList(),
                "TubeMill7" => _context.TubeMill_7.OrderByDescending(t => t.Timestamp).Take(5000).ToList(),
                _ => _context.TubeMill_3.ToList()
            };

           

           // Map TubeMill data
            var tubeMillData = data.Select(t => new UnifiedDashboardItem
            {
                Timestamp = t.Timestamp,
                Voltage_Percentage = t.Voltage_Percentage != null ? Math.Round((double)t.Voltage_Percentage, 2) : (double?)null,
                Current_Percentage = t.Current_Percentage != null ? Math.Round((double)t.Current_Percentage, 2) : (double?)null,
                Actual_kW = t.Actual_kW != null ? Math.Round((double)t.Actual_kW, 2) : (double?)null,
                Actual_Frequency = t.Actual_Frequency != null ? Math.Round((double)t.Actual_Frequency, 2) : (double?)null,
                LineSpeed = t.LineSpeed != null ? Math.Round((double)t.LineSpeed, 2) : (double?)null
            }).Where(u => u.Timestamp != default(DateTime)).ToList();

          
            // Pass data to the view model
            var viewModel = new CombinedViewModel
            {
                TubeMills = data,
             

            };

            ViewBag.SelectedTable = tableName;

            
          
            return View(viewModel);
        }
