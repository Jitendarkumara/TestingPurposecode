TimeSpan duration = dateTimeEndTime.Value - dateTimeStartTime.Value;
textDuration.Text = duration.TotalMinutes.ToString("F0") + " minutes";