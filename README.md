var tableName = "M03";
var unifiedData = _context.Alarm_Output
    .Where(u => u.Mill != null && u.Mill.Equals(tableName, StringComparison.OrdinalIgnoreCase))
    .OrderByDescending(u => u.Timestamp)
    .Select(millData => new
    {
        millData.Timestamp,
        millData.Voltage,
        millData.Currents,
        millData.Power,
        millData.Frequency,
        millData.Speed,
        millData.OD,
        millData.Grade,
        millData.Mill,
        millData.alarm,
        millData.Erroneous_Parameter
    })
    .Take(1000)
    .ToList();