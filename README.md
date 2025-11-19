public DataTable GetActiveCoilEvents()
{
    DataTable dtEventTable = new DataTable();

    try
    {
        if (connection.State != ConnectionState.Open)
            connection.Open();

        using (OracleDataAdapter evtda = 
               new OracleDataAdapter("SELECT ID_APP_TAG_EVENT, COIL_ID, WIDTH, THICKNESS, P_MODE FROM T_EVENT_TRACKING", connection))
        {
            evtda.Fill(dtEventTable);
        }

        if (dtEventTable == null || dtEventTable.Rows.Count == 0)
        {
            ResetCoilPositions();
            return dtEventTable;
        }

        // Defensive reading – only read rows if they exist
        ReadValue(dtEventTable, 0, v => {
            CoilPosition.L1_APP_POR_SELECTION = v;
            CoilPosition.ProcessMode = dtEventTable.Rows[0]["P_MODE"]?.ToString();
        });

        ReadValue(dtEventTable, 1, v => CoilPosition.L1_WELD_BR2 = v);
        ReadValue(dtEventTable, 2, v => CoilPosition.L1_WELD_BR3 = v);
        ReadValue(dtEventTable, 3, v => CoilPosition.L1_WELD_BR8 = v);
        ReadValue(dtEventTable, 4, v => CoilPosition.L1_COIL_EXT = v);

        // Build tblActiveCoils safely
        if (dtEventTable.Rows.Count > 0)
        {
            if (CoilPosition.tblActiveCoils != null)
                CoilPosition.tblActiveCoils.Clear();

            var filtered = dtEventTable.AsEnumerable()
                .Where(r => !string.IsNullOrWhiteSpace(r.Field<string>("COIL_ID")));

            if (filtered.Any())
                CoilPosition.tblActiveCoils = filtered.CopyToDataTable();
        }

        return dtEventTable;
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error: " + ex.Message, "GetActiveCoilEvents Error",
            MessageBoxButtons.OK, MessageBoxIcon.Error);

        return null;
    }
    finally
    {
        if (connection.State == ConnectionState.Open)
            connection.Close();
    }
}