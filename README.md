public int CheckTimeDuration(DateTime StartTime, DateTime EndTime)
{
    Db.DatabaseConnect();

    string query = @"
        SELECT TCIP_PRODUCT_COIL, TCIP_CIL_START_TIME, TCIP_CIL_END_TIME 
        FROM T_COL_COIL_INFO_PDO 
        WHERE 
            (TCIP_CIL_START_TIME BETWEEN :StartTime AND :EndTime)
            OR 
            (TCIP_CIL_END_TIME BETWEEN :StartTime AND :EndTime)
            OR 
            (:StartTime BETWEEN TCIP_CIL_START_TIME AND TCIP_CIL_END_TIME)
            OR 
            (:EndTime BETWEEN TCIP_CIL_START_TIME AND TCIP_CIL_END_TIME)
    ";

    using (OracleCommand cmd = new OracleCommand(query, Db.Con))
    {
        cmd.Parameters.Add(new OracleParameter("StartTime", StartTime));
        cmd.Parameters.Add(new OracleParameter("EndTime", EndTime));

        using (OracleDataAdapter da = new OracleDataAdapter(cmd))
        {
            DataTable dt = new DataTable();
            da.Fill(dt);

            if (dt.Rows.Count > 0)
            {
                string coilId = dt.Rows[0]["TCIP_PRODUCT_COIL"].ToString();
                string start_time = dt.Rows[0]["TCIP_CIL_START_TIME"].ToString();
                string end_time = dt.Rows[0]["TCIP_CIL_END_TIME"].ToString();

                MessageBox.Show($"{coilId} already exists between {start_time} and {end_time}");
                return 1;
            }
        }
    }

    return 0;
}
string query = @"
    SELECT TCIP_PRODUCT_COIL, TCIP_CIL_START_TIME, TCIP_CIL_END_TIME 
    FROM T_COL_COIL_INFO_PDO 
    WHERE 
        (TCIP_CIL_START_TIME BETWEEN TO_TIMESTAMP(:StartTime, 'DD-MON-YY HH.MI.SS.FF AM') AND TO_TIMESTAMP(:EndTime, 'DD-MON-YY HH.MI.SS.FF AM'))
        OR 
        (TCIP_CIL_END_TIME BETWEEN TO_TIMESTAMP(:StartTime, 'DD-MON-YY HH.MI.SS.FF AM') AND TO_TIMESTAMP(:EndTime, 'DD-MON-YY HH.MI.SS.FF AM'))
        OR 
        (TO_TIMESTAMP(:StartTime, 'DD-MON-YY HH.MI.SS.FF AM') BETWEEN TCIP_CIL_START_TIME AND TCIP_CIL_END_TIME)
        OR 
        (TO_TIMESTAMP(:EndTime, 'DD-MON-YY HH.MI.SS.FF AM') BETWEEN TCIP_CIL_START_TIME AND TCIP_CIL_END_TIME)
";