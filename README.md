string shift1;
TimeSpan shift = endtime.TimeOfDay;
if (shift >= new TimeSpan(6, 0, 0) && shift < new TimeSpan(14, 0, 0))
{
    shift1 = "A";

}
else if (shift >= new TimeSpan(14, 0, 0) && shift < new TimeSpan(22, 0, 0))
{
    shift1 = "B";
}
else
{
    shift1 = "C";
}
