public IActionResult Dashboard(string tableName = "TubeMill3", DateTime Tdate)
{
    ViewBag.AvailableTables = new List<string>
    {
        "TubeMill1",
        "TubeMill2",
        "TubeMill3",
        "TubeMill4",
        "TubeMill7"
    };

    // Define the beginning and end of the selected date
    DateTime selectedDayStart = Tdate.Date;
    DateTime selectedDayEnd = selectedDayStart.AddDays(1);

    // Dynamically fetch TubeMill data based on the selected table and selected date range.
    IEnumerable<dynamic> data = tableName switch
    {
        "TubeMill1" => _context.TubeMill_1
                                .Where(t => t.Timestamp >= selectedDayStart && t.Timestamp < selectedDayEnd)
                                .OrderByDescending(t => t.Timestamp)
                                .ToList(),
        "TubeMill2" => _context.TubeMill_2
                                .Where(t => t.Timestamp >= selectedDayStart && t.Timestamp < selectedDayEnd)
                                .OrderByDescending(t => t.Timestamp)
                                .ToList(),
        "TubeMill3" => _context.TubeMill_3
                                .Where(t => t.Timestamp >= selectedDayStart && t.Timestamp < selectedDayEnd)
                                .OrderByDescending(t => t.Timestamp)
                                .ToList(),
        "TubeMill4" => _context.TubeMill_4
                                .Where(t => t.Timestamp >= selectedDayStart && t.Timestamp < selectedDayEnd)
                                .OrderByDescending(t => t.Timestamp)
                                .ToList(),
        "TubeMill7" => _context.TubeMill_7
                                .Where(t => t.Timestamp >= selectedDayStart && t.Timestamp < selectedDayEnd)
                                .OrderByDescending(t => t.Timestamp)
                                .ToList(),
        _ => _context.TubeMill_3
                     .Where(t => t.Timestamp >= selectedDayStart && t.Timestamp < selectedDayEnd)
                     .OrderByDescending(t => t.Timestamp)
                     .ToList()
    };

    // Map the data into the unified dashboard model
    var tubeMillData = data.Select(t => new UnifiedDashboardItem
    {
        Timestamp = t.Timestamp,
        Voltage_Percentage = t.Voltage_Percentage != null ? Math.Round((double)t.Voltage_Percentage, 2) : (double?)null,
        Current_Percentage = t.Current_Percentage != null ? Math.Round((double)t.Current_Percentage, 2) : (double?)null,
        Actual_kW = t.Actual_kW != null ? Math.Round((double)t.Actual_kW, 2) : (double?)null,
        Actual_Frequency = t.Actual_Frequency != null ? Math.Round((double)t.Actual_Frequency, 2) : (double?)null,
        LineSpeed = t.LineSpeed != null ? Math.Round((double)t.LineSpeed, 2) : (double?)null
    }).Where(u => u.Timestamp != default(DateTime)).ToList();

    // Prepare the view model, passing the complete data set
    var viewModel = new CombinedViewModel
    {
        TubeMills = data
    };

    ViewBag.SelectedTable = tableName;

    return View(viewModel);
}