var filteredData = Model.Alarm.Where(item => item.Mill[0] == selectedMillNo).ToList();
