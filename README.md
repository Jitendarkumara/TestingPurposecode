var rowStopTimePicker = new DateTimePicker
{
    Format = DateTimePickerFormat.Custom,
    CustomFormat = "yyyy-MM-dd HH:mm:ss",
    Width = 150,
    MinDate = dtpDelayStartTime.Value,
    MaxDate = dtpDelayStopTime.Value,
    Font = new Font("Microsoft Sans Serif", 8.25F, FontStyle.Regular)
};

// 👇 Set initial value from parent DateTimePicker
rowStopTimePicker.Value = dtpDelayStartTime.Value;