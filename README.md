private void UpdateData(string Coilid, string TagName, string EventID)
{
    try
    {
        Db.DatabaseConnect(); // Ensure this properly initializes Db.Con (OracleConnection)

        string query = "UPDATE T_Event_Tracking SET COIL_ID = :Coilid, TAG_NAME = :TagName WHERE ID_APP_TAG_EVENT = :EventID";

        using (OracleCommand cmd = new OracleCommand(query, Db.Con))
        {
            cmd.Parameters.Add(new OracleParameter(":Coilid", Coilid));
            cmd.Parameters.Add(new OracleParameter(":TagName", TagName));
            cmd.Parameters.Add(new OracleParameter(":EventID", EventID));

            Db.Con.Open();
            int rowsAffected = cmd.ExecuteNonQuery();
            Db.Con.Close();

            if (rowsAffected > 0)
            {
                MessageBox.Show("Record Updated Successfully!", "Success", MessageBoxButtons.OK, MessageBoxIcon.Information);
            }
            else
            {
                MessageBox.Show("No record updated. Please check the ID.", "Warning", MessageBoxButtons.OK, MessageBoxIcon.Warning);
            }
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error: " + ex.Message, "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
    finally
    {
        if (Db.Con.State == ConnectionState.Open)
            Db.Con.Close();
    }
}