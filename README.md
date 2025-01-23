    var unifiedData = _context.Alarm_Output.OrderByDescending(u => u.Timestamp).Select(millData => new
    {
        
     millData.Timestamp,
       millData.Voltage,
       millData.Currents,
        millData.Power,
        millData.Frequency,
        millData.Speed,
        millData.OD,
        // Thickness = millData?.Thickness,
        millData.Grade,
        //   Customer = millData?.Customer,
        millData.Mill,
       millData.alarm,
        millData.Erroneous_Parameter

        // };
    }).Where(u=>u.Mill==tableName).Take(1000).ToList();
