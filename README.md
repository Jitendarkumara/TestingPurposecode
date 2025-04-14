public IActionResult GetChartData(DateTime selectedDate, string selectedShift, string selectedMill)
{
    ViewBag.AvailableTables = new List<string>
    {
        "TubeMill1",
        "TubeMill2",
        "TubeMill3",
        "TubeMill4",
        "TubeMill7"
    };

    // Determine shift times
    TimeSpan shiftStart, shiftEnd;
    selectedDate = Convert.ToDateTime(selectedDate.ToString("yyyy-MM-dd"));

    if (selectedShift == "A")
    {
        shiftStart = new TimeSpan(6, 0, 0);  // 06:00 AM
        shiftEnd = new TimeSpan(14, 0, 0);   // 02:00 PM
    }
    else if (selectedShift == "B")
    {
        shiftStart = new TimeSpan(14, 0, 0);  // 02:00 PM
        shiftEnd = new TimeSpan(22, 0, 0);    // 10:00 PM
    }
    else if (selectedShift == "C")
    {
        shiftStart = new TimeSpan(22, 0, 0);  // 10:00 PM
        shiftEnd = new TimeSpan(6, 0, 0);     // 06:00 AM (next day)
    }
    else
    {
        shiftStart = new TimeSpan(0, 0, 0);  
        shiftEnd = new TimeSpan(23, 59, 59);
    }

    // Compute the datetime range
    DateTime selectedDayStart = selectedDate.Date + shiftStart;
    DateTime selectedDayEnd = selectedDate.Date + shiftEnd;
    
    // For shifts that cross midnight, adjust if needed (this code does not show that, so adjust as per your needs)

    // Dynamically fetch TubeMill data based on selected table and date range
    IEnumerable<dynamic> data = selectedMill switch
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

    // Map data to a unified model (if needed for charts)
    var tubeMillData = data.Select(t => new UnifiedDashboardItem
    {
        Timestamp = t.Timestamp,
        Voltage_Percentage = t.Voltage_Percentage != null ? Math.Round((double)t.Voltage_Percentage, 2) : (double?)null,
        Current_Percentage = t.Current_Percentage != null ? Math.Round((double)t.Current_Percentage, 2) : (double?)null,
        Actual_kW = t.Actual_kW != null ? Math.Round((double)t.Actual_kW, 2) : (double?)null,
        Actual_Frequency = t.Actual_Frequency != null ? Math.Round((double)t.Actual_Frequency, 2) : (double?)null,
        LineSpeed = t.LineSpeed != null ? Math.Round((double)t.LineSpeed, 2) : (double?)null
    })
    .Where(u => u.Timestamp != default(DateTime))
    .ToList();

    // Return JSON instead of a view.
    return Json(tubeMillData);
}