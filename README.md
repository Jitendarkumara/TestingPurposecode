public DataTable GetActiveCoilEvents()
{
    DataTable dtEventTable = new DataTable();

    try
    {
        if (connection.State != ConnectionState.Open)
            connection.Open();

        using (OracleDataAdapter da =
            new OracleDataAdapter("SELECT ID_APP_TAG_EVENT, COIL_ID, WIDTH, THICKNESS, P_MODE FROM T_EVENT_TRACKING", connection))
        {
            da.Fill(dtEventTable);
        }

        // If no rows exist → avoid indexing crashes
        if (dtEventTable.Rows.Count == 0)
            return dtEventTable;

        // Safely assign positions (only if those row indexes exist)
        if (dtEventTable.Rows.Count > 0)
        {
            CoilPosition.L1_APP_POR_SELECTION =
                dtEventTable.Rows[0]["COIL_ID"] == DBNull.Value ? "" : dtEventTable.Rows[0]["COIL_ID"].ToString();

            CoilPosition.ProcessMode =
                dtEventTable.Rows[0]["P_MODE"] == DBNull.Value ? "" : dtEventTable.Rows[0]["P_MODE"].ToString();
        }

        if (dtEventTable.Rows.Count > 1)
        {
            CoilPosition.L1_WELD_BR2 =
                dtEventTable.Rows[1]["COIL_ID"] == DBNull.Value ? "" : dtEventTable.Rows[1]["COIL_ID"].ToString();
        }

        if (dtEventTable.Rows.Count > 2)
        {
            CoilPosition.L1_WELD_BR3 =
                dtEventTable.Rows[2]["COIL_ID"] == DBNull.Value ? "" : dtEventTable.Rows[2]["COIL_ID"].ToString();
        }

        if (dtEventTable.Rows.Count > 3)
        {
            CoilPosition.L1_WELD_BR8 =
                dtEventTable.Rows[3]["COIL_ID"] == DBNull.Value ? "" : dtEventTable.Rows[3]["COIL_ID"].ToString();
        }

        if (dtEventTable.Rows.Count > 4)
        {
            CoilPosition.L1_COIL_EXT =
                dtEventTable.Rows[4]["COIL_ID"] == DBNull.Value ? "" : dtEventTable.Rows[4]["COIL_ID"].ToString();
        }

        // Build Active Coils Table (unique coil_id)
        if (CoilPosition.tblActiveCoils != null)
            CoilPosition.tblActiveCoils.Clear();

        var grouped = dtEventTable.AsEnumerable()
            .Where(r => !string.IsNullOrWhiteSpace(r.Field<string>("COIL_ID")))
            .GroupBy(r => r.Field<string>("COIL_ID"))
            .Select(g => g.First());

        if (grouped.Any())
        {
            CoilPosition.tblActiveCoils = grouped.CopyToDataTable();
        }
        else
        {
            // Create empty table with same schema
            CoilPosition.tblActiveCoils = dtEventTable.Clone();
        }

        return dtEventTable;
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error loading coil events: " + ex.Message,
                        "GetActiveCoilEvents Error",
                        MessageBoxButtons.OK,
                        MessageBoxIcon.Error);

        return new DataTable(); // return empty table instead of null
    }
    finally
    {
        if (connection.State == ConnectionState.Open)
            connection.Close();
    }
}