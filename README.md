        private void FillDelayGridShift(string selectedShift, DateTime selectedDate)
        {
            // Construct the SQL query with parameterization
            string query = "SELECT DELAY_ID, SHIFT, COIL_NUMBER, LINE_STOP_TIME, LINE_START_TIME, " +
                           "DELAY_DURATION, REASON_CODE, OPERATOR_ID, REASON_DESCRIPTION, REMARKS, AGENCY_CODE, TOM " +
                           "FROM T_DELAY_SHEET_TEM, T_SHIFT " +
                           "WHERE T_DELAY_SHEET_TEM.SHIFT = T_SHIFT.SHIFT_NAME AND " +
                           "T_DELAY_SHEET_TEM.SHIFT = :Shift AND TRUNC(LINE_START_TIME) = TRUNC(:SelectedDate) " +
                           "ORDER BY LINE_STOP_TIME DESC";

            try
            {
                // Connect to the database
                Db.DatabaseConnect();

                // Prepare the OracleDataAdapter with the query
                using (OracleDataAdapter da = new OracleDataAdapter(query, Db.Con))
                {
                    // Add parameters to the query
                    da.SelectCommand.Parameters.Add("Shift", OracleDbType.Varchar2).Value = selectedShift;
                    da.SelectCommand.Parameters.Add("SelectedDate", OracleDbType.Date).Value = selectedDate.Date;

                    // Fill the DataTable with the results
                    dtshift = new DataTable();
                    da.Fill(dtshift);
                    dgvDelay.DataSource = dtshift; // Bind the DataTable to the DataGridView
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("An error occurred: " + ex.Message);
            }
            finally
            {
                Db.ConClose(); // Ensure the connection is closed
            }

            // Optionally set DataGridView properties
            dgvDelay.AutoGenerateColumns = false; // Ensure columns are not auto-generated if you want to define them manually
        }
