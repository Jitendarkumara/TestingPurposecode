DateTime startDate = dateTimePickerFromDt.Value;
DateTime endDate = dateTimePickerToDt.Value;
        
// Check if the end date is greater than or equal to the start date
if (startDate > endDate)
{
    MessageBox.Show("End date cannot be earlier than start date.");
    return;
}
