 TimeSpan shift = endtime.TimeOfDay;
 if (shift >= new TimeSpan(6, 0, 0) && shift < new TimeSpan(14, 0, 0))
 {
     txtShift.Text = "A";

 }
 else if (shift >= new TimeSpan(14, 0, 0) && shift < new TimeSpan(22, 0, 0))
 {
     txtShift.Text = "B";
 }
 else
 {
     txtShift.Text = "C";
 }
