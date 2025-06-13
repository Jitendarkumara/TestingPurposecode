using System;
using System.Globalization;
using System.Windows.Forms;
using Oracle.ManagedDataAccess.Client; // Ensure this is the Oracle library you're using

private void button1_Click(object sender, EventArgs e)
{
    if (dateTimeEndTime.Value > DateTime.Now)
    {
        MessageBox.Show("End Time should not be greater than current time.");
        return;
    }

    if (DateTime.Now - dateTimeEndTime.Value > TimeSpan.FromHours(24))
    {
        MessageBox.Show("GR Time has expired");
        return;
    }

    if (string.IsNullOrWhiteSpace(textProductCoilId.Text))
    {
        MessageBox.Show("Product coil cannot be blank.");
        return;
    }

    if (string.IsNullOrWhiteSpace(textDuration.Text))
    {
        MessageBox.Show("Duration cannot be blank.");
        return;
    }

    if (!float.TryParse(textDuration.Text, out float duration) || duration < 1)
    {
        MessageBox.Show("Duration must be at least 60 seconds.");
        return;
    }

    try
    {
        DateTime START = dateTimeStartTime.Value;
        DateTime END = dateTimeEndTime.Value;

        if (END > DateTime.Now)
        {
            MessageBox.Show("End time should be less than or equal to current date time.");
            return;
        }

        // Connect to the database
        Db.DatabaseConnect();

        // Check if the product coil ID already exists
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
        string insertQuery = @"INSERT INTO T_COL_COIL_INFO_PDO
(TCPI_ACTUAL_WT, TCIP_PRODUCT_COIL, TCIP_INPUT_COIL, TCPI_PROD_COIL_WDT,
 TCIP_CIL_START_TIME, TCIP_CIL_END_TIME, TCPI_DURATION,
 TCPI_PROD_COIL_LENGTH, TCPI_PROD_COIL_IDIA, TCPI_PROD_COIL_ODIA,
 TCPI_NXPROC_HOLD_REQ, TCPI_NXPROC_HOLD_RES1, TCPI_PROD_COIL_THICK_AFT_PRINT,
 REMARKS, MANUAL_REASON, PDO_FLAG)
VALUES
(:actualWeight, :productCoilId, :inputCoilId, :coilWidth,
 :startTime, :endTime, :duration,
 :coilLength, :innerDiameter, :outerDiameter,
 :holdRequest, :holdReason, :actualThickness,
 :remarks, :manualReason, :pdoFlag)";

        using (OracleCommand cmd = new OracleCommand(insertQuery, Db.Con))
        {
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
            cmd.Parameters.Add(new OracleParameter("manualReason", cmbMan_Reason.Text));
            cmd.Parameters.Add(new OracleParameter("pdoFlag", 'M'));

            int rowsAffected = cmd.ExecuteNonQuery();

            if (rowsAffected > 0)
            {
                MessageBox.Show("Data inserted successfully!");
            }
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