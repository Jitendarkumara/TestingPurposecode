public List<Tuple<DateTime, DateTime>> GetDaughterCoilTimestamps(string coil_id)
{
    var events = DBConnectivity.GetDataTable($@"
        SELECT DATE_TIME, ID_APP_TAG 
        FROM T_EVENT_LOG 
        WHERE ID_APP_TAG IN ('L1_COIL_START', 'EXIT_SHEAR') 
        AND VALUE = 1 AND COIL_ID = '{coil_id}'
        ORDER BY DATE_TIME");

    List<Tuple<DateTime, DateTime>> daughterTimestamps = new();
    DateTime startTime = DateTime.MinValue;

    foreach (DataRow row in events.Rows)
    {
        string tag = row["ID_APP_TAG"].ToString();
        DateTime time = Convert.ToDateTime(row["DATE_TIME"]);

        if (tag == "L1_COIL_START")
        {
            startTime = time; // First mother coil start
        }
        else if (tag == "EXIT_SHEAR" && startTime != DateTime.MinValue)
        {
            // Create a daughter coil time window
            daughterTimestamps.Add(new Tuple<DateTime, DateTime>(startTime, time));
            startTime = time; // Next daughter coil starts right after this
        }
    }

    return daughterTimestamps;
}