private void button1_Click(object sender, EventArgs e)
{
    // Validate that End Time is not in the future
    if (dateTimeEndTime.Value > DateTime.Now)
    {
        MessageBox.Show("End Time should not be greater than current time.");
        return;
    }

    // Validate GR Time is within 24 hours
    if (DateTime.Now - dateTimeEndTime.Value > TimeSpan.FromHours(24))
    {
        MessageBox.Show("GR Time has expired");
        return;
    }

    // Validate that Product Coil ID is not empty
    if (string.IsNullOrWhiteSpace(textProductCoilId.Text))
    {
        MessageBox.Show("Product coil cannot be blank.");
        return;
    }

    // Validate that Duration is not empty
    if (string.IsNullOrWhiteSpace(textDuration.Text))
    {
        MessageBox.Show("Duration cannot be blank.");
        return;
    }

    // Validate that Duration is greater than or equal to 1 minute
    if (Convert.ToInt32(float.Parse(textDuration.Text)) < 1)
    {
        MessageBox.Show("Duration cannot be less than 60 seconds.");
        return;
    }

    try
    {
        DateTime START = dateTimeStartTime.Value;
        DateTime END = dateTimeEndTime.Value;

        if (END <= DateTime.Now)
        {
            // Connect to the database
            Db.DatabaseConnect();

            // Check if product coil ID already exists
            string checkQuery = "SELECT COUNT(*) FROM T_COL_COIL_INFO_PDO WHERE TCIP_PRODUCT_COIL = :productCoilId";

            using (OracleCommand checkCmd = new OracleCommand(checkQuery, Db.Con))
            {
                checkCmd.Parameters.Add(new OracleParameter("productCoilId", textProductCoilId.Text));

                int count = Convert.ToInt32(checkCmd.ExecuteScalar());

                if (count > 0)
                {
                    MessageBox.Show("Already exists PDO for this Product Coil ID.");
                    return;
                }
            }

            // SQL Insert Query
            string insertQuery = @"
                INSERT INTO T_COL_COIL_INFO_PDO
                (
                    TCPI_ACTUAL_WT, TCIP_PRODUCT_COIL, TCIP_INPUT_COIL, TCPI_PROD_COIL_WDT,
                    TCIP_CIL_START_TIME, TCIP_CIL_END_TIME, TCPI_DURATION,
                    TCPI_PROD_COIL_LENGTH, TCPI_PROD_COIL_IDIA, TCPI_PROD_COIL_ODIA,
                    TCPI_NXPROC_HOLD_REQ, TCPI_NXPROC_HOLD_RES1, TCPI_PROD_COIL_THICK_AFT_PRINT,
                    REMARKS, MANUAL_REASON, PDO_FLAG
                )
                VALUES
                (
                    :actualWeight, :productCoilId, :inputCoilId, :coilWidth,
                    :startTime, :endTime, :duration,
                    :coilLength, :innerDiameter, :outerDiameter,
                    :holdRequest, :holdReason, :actualThickness,
                    :remarks, :MANUAL_REASON, :PDO_FLAG
                )";

            using (OracleCommand cmd = new OracleCommand(insertQuery, Db.Con))
            {
                // Bind parameters
                cmd.Parameters.Add(new OracleParameter("actualWeight", textActWeight.Text));
                cmd.Parameters.Add(new OracleParameter("productCoilId", textProductCoilId.Text));
                cmd.Parameters.Add(new OracleParameter("inputCoilId", textInputCoilId.Text));
                cmd.Parameters.Add(new OracleParameter("coilWidth", textInpiutWidth.Text));
                cmd.Parameters.Add(new OracleParameter("startTime", START));
                cmd.Parameters.Add(new OracleParameter("endTime", END));
                cmd.Parameters.Add(new OracleParameter("duration", textDuration.Text));
                cmd.Parameters.Add(new OracleParameter("coilLength", textlength.Text));
                cmd.Parameters.Add(new OracleParameter("innerDiameter", textInnerDia.Text));
                cmd.Parameters.Add(new OracleParameter("outerDiameter", textOdia.Text));
                cmd.Parameters.Add(new OracleParameter("holdRequest", CMBHoldRequest.Text));
                cmd.Parameters.Add(new OracleParameter("holdReason", textHoldReason.Text));
                cmd.Parameters.Add(new OracleParameter("actualThickness", textActThickness.Text));
                cmd.Parameters.Add(new OracleParameter("remarks", textRemarks.Text));
                cmd.Parameters.Add(new OracleParameter("MANUAL_REASON", cmbMan_Reason.Text));
                cmd.Parameters.Add(new OracleParameter("PDO_FLAG", 'M'));

                // Execute insert
                int rowsAffected = cmd.ExecuteNonQuery();

                if (rowsAffected > 0)
                {
                    MessageBox.Show("Data inserted successfully!");
                }
            }
        }
        else
        {
            MessageBox.Show("End time should be less than or equal to current date and time.");
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error inserting data: " + ex.Message);
    }
    finally
    {
        Db.ConClose();
    }
}