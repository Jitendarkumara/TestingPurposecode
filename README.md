string connectionString = Db.ConnectioString; // Use your database connection string

using (OracleConnection connection = new OracleConnection(connectionString))
{
    try
    {
        connection.Open();

        string query = @"select CGD_ID_COIL,
                           CGD_MK_CUSTOMER,
                               CGD_TS_START,
                                 CGD_TS_END,
                               CGD_WORK_DUR,
                           CGD_ID_COIL_MTHR,
                                CGD_TDC_NO,
                              CGD_CD_GRADE,
                              CGD_CD_QLTY,
                                 CGD_IDIA,
                                CGD_ODIA,
                               CGD_MS_ACTL,
                              CGD_MS_CAL   from T_PDO_INFO
                  WHERE CGD_ID_COIL_MTHR= :CoilID";

        using (OracleCommand command = new OracleCommand(query, connection))
        {
           

            OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);

            command.Parameters.Add(new OracleParameter("CoilID", CoilID));
            DataTable DtPDO = new DataTable();
            da.Fill(DtPDO);
            

                dataGridView.DataSource = DtPDO;

                // Adjust header and column settings
                dataGridView.AutoSizeColumnsMode = DataGridViewAutoSizeColumnsMode.AllCells;
                dataGridView.ColumnHeadersHeightSizeMode = DataGridViewColumnHeadersHeightSizeMode.AutoSize;
                dataGridView.AllowUserToResizeColumns = true;

                // Refresh to apply changes
                dataGridView.Refresh();
            
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show($"Error loading data: {ex.Message}");
    }
}
