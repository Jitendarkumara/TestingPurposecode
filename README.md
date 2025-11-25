try
{
    string ConString = System.Configuration.ConfigurationManager
                        .ConnectionStrings["ServiceURL"].ToString();

    using (OracleConnection Con = new OracleConnection(ConString))
    {
        Con.Open();

        // Check if the Mother Coil ID already exists
        string checkQuery = "SELECT COUNT(*) FROM T_COIL_PRODUCED WHERE MOTHER_COIL = :coilId";

        using (OracleCommand checkCmd = new OracleCommand(checkQuery, Con))
        {
            checkCmd.Parameters.Add("coilId", OracleDbType.Varchar2, 30).Value =
                txtMthrBatch.Text?.Substring(0, Math.Min(txtMthrBatch.Text.Length, 30));

            int count = Convert.ToInt32(checkCmd.ExecuteScalar());

            if (count > 0)
            {
                MessageBox.Show("This Mother Coil ID already exists in T_COIL_PRODUCED. Transfer aborted.");
                return;
            }
        }

        // Proceed with stored procedure if not found
        string SProc = "TRANSFER_TO_MES_PLANNING_ENTRYSIDE";

        using (OracleCommand cmd = new OracleCommand(SProc, Con))
        {
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.Add("p_mother_coil_id", OracleDbType.Varchar2, 30).Value =
                txtMthrBatch.Text?.Substring(0, Math.Min(txtMthrBatch.Text.Length, 30));

            cmd.Parameters.Add("p_Rejected_By", OracleDbType.Varchar2, 30).Value =
                txtRejected_By.Text?.Substring(0, Math.Min(txtRejected_By.Text.Length, 30));

            cmd.Parameters.Add("p_remarks", OracleDbType.Varchar2, 200).Value =
                txtRemarks.Text?.Substring(0, Math.Min(txtRemarks.Text.Length, 200));

            cmd.ExecuteNonQuery();
        }

        MessageBox.Show("Data has been successfully transferred to MES.");
    }
}
catch (OracleException oex)
{
    MessageBox.Show($"Oracle Error: {oex.Message}");
}
catch (Exception ex)
{
    MessageBox.Show($"Failed to transfer data to MES: {ex.Message}");
}