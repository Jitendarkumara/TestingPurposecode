public DateTime ConvertShiftToDateTime(string selectedShift, DateTime referenceDate)
{
    TimeSpan shiftStartTime;

    switch (selectedShift.ToUpper())
    {
        case "A":
            shiftStartTime = new TimeSpan(6, 0, 0);  // 6:00 AM
            break;
        case "B":
            shiftStartTime = new TimeSpan(14, 0, 0); // 2:00 PM
            break;
        case "C":
            shiftStartTime = new TimeSpan(22, 0, 0); // 10:00 PM
            break;
        default:
            throw new ArgumentException("Invalid shift. Use A, B, or C.");
    }

    return referenceDate.Date.Add(shiftStartTime);
}