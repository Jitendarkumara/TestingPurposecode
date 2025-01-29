public IActionResult GetFilteredData(DateTime selectedDate, string selectedShift, string selectedMill)
{
    using (var context = new YourDbContext())
    {
        // Define shift timing
        TimeSpan shiftStart, shiftEnd;

        if (selectedShift == "Morning")
        {
            shiftStart = new TimeSpan(6, 0, 0);  // 06:00 AM
            shiftEnd = new TimeSpan(14, 0, 0);   // 02:00 PM
        }
        else if (selectedShift == "Evening")
        {
            shiftStart = new TimeSpan(14, 0, 0);  // 02:00 PM
            shiftEnd = new TimeSpan(22, 0, 0);    // 10:00 PM
        }
        else if (selectedShift == "Night")
        {
            shiftStart = new TimeSpan(22, 0, 0);  // 10:00 PM
            shiftEnd = new TimeSpan(6, 0, 0);     // 06:00 AM (next day)
        }
        else
        {
            return Json(new { success = false, message = "Invalid shift selected." });
        }

        // Convert selectedDate to the start and end datetime
        DateTime shiftStartDateTime = selectedDate.Date + shiftStart;
        DateTime shiftEndDateTime = selectedDate.Date + shiftEnd;

        // Special case for Night shift (crosses midnight)
        if (selectedShift == "Night")
        {
            shiftEndDateTime = shiftEndDateTime.AddDays(1);
        }

        // Query data from Entity Framework
        var filteredData = context.Alarm
            .Where(item => item.Mill == selectedMill &&
                           item.Timestamp >= shiftStartDateTime &&
                           item.Timestamp < shiftEndDateTime)
            .ToList();

        return Json(filteredData);
    }
}