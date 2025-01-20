 var unifiedData = _context.Alarm_Output.OrderByDescending(u => u.Timestamp).Select(millData => new
 {
     // Find the most recent TubeInputParameter before or at the current TubeMill timestamp
     // var matchingInput = tubeInputParameters.LastOrDefault(p => p.CreatedOn <= millData.Timestamp);

     // Merge TubeInputParameters into the TubeMill data
     //return new Alarm_Output
     //{
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
 }).Take(100).ToList();
