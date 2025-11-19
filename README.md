public DataTable GetActiveCoilEvents()
{
    DataTable dtEventTable = new DataTable();

    try
    {
        if (connection.State != ConnectionState.Open)
            connection.Open();

        using (OracleDataAdapter da = new OracleDataAdapter(
            "SELECT ID_APP_TAG_EVENT, COIL_ID, WIDTH, THICKNESS, P_MODE FROM T_EVENT_TRACKING",
            connection))
        {
            da.Fill(dtEventTable);
        }

        if (dtEventTable == null || dtEventTable.Rows.Count == 0)
            return null;

        // Safe helper method
        string GetValue(DataTable dt, int row, int col)
        {
            if (dt.Rows.Count > row && dt.Rows[row][col] != DBNull.Value)
                return dt.Rows[row][col].ToString();
            return "";
        }

        // Extract values SAFELY
        CoilPosition.L1_APP_POR_SELECTION = GetValue(dtEventTable, 0, 1);
        CoilPosition.ProcessMode           = GetValue(dtEventTable, 0, 4);
        CoilPosition.L1_WELD_BR2           = GetValue(dtEventTable, 1, 1);
        CoilPosition.L1_WELD_BR3           = GetValue(dtEventTable, 2, 1);
        CoilPosition.L1_WELD_BR8           = GetValue(dtEventTable, 3, 1);
        CoilPosition.L1_COIL_EXT           = GetValue(dtEventTable, 4, 1);

        // Store active coils
        if (CoilPosition.tblActiveCoils != null)
            CoilPosition.tblActiveCoils.Clear();

        CoilPosition.tblActiveCoils =
            dtEventTable.AsEnumerable()
            .Where(r => !string.IsNullOrWhiteSpace(r.Field<string>("COIL_ID")))
            .CopyToDataTable();

        return dtEventTable;
    }
    catch (Exception ex)
    {
        MessageBox.Show("GetActiveCoilEvents Error: " + ex.Message);
        return null;
    }
    finally
    {
        if (connection.State == ConnectionState.Open)
            connection.Close();
    }
}