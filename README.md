private string GetL3ReadFlag(string daughterCoilId)
{
    string l3ReadFlag = string.Empty;

    string query = "SELECT l3_read_flag FROM T_PDO_INFO WHERE CGD_ID_COIL = :DaughterCoilId";

    // Check and open the connection if needed
    if (Db.Con.State != ConnectionState.Open)
    {
        Db.Con.Open();
    }

    using (OracleCommand command = new OracleCommand(query, Db.Con))
    {
        command.Parameters.Add(new OracleParameter("DaughterCoilId", daughterCoilId));

        object result = command.ExecuteScalar();
        if (result != null)
        {
            l3ReadFlag = result.ToString();
        }
    }

    return l3ReadFlag;
}