   var filteredData = _context.Alarm_Output
       .Where(item => item.Mill == selectedMill &&
                      item.Timestamp >= shiftStartDateTime &&
                      item.Timestamp <= shiftEndDateTime)
       .ToList();
