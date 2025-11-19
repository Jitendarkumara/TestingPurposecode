public int UpdateTrackingPage(DataTable dtnewTrack)
{
    int success = 0;

    try
    {
        DataTable dtTrack = GetlineData();
        if (dtTrack == null || dtnewTrack == null)
            return 0;

        if (connection.State != ConnectionState.Open)
            connection.Open();

        using (OracleTransaction trans = connection.BeginTransaction())
        using (OracleCommand cmd = connection.CreateCommand())
        {
            cmd.Transaction = trans;
            cmd.CommandType = CommandType.Text;

            // Use parameters instead of string concatenation
            cmd.CommandText =
                @"UPDATE T_GP2_TRACKING_PAGE 
                  SET COIL_ID = :coilid, 
                      WIDTH = :width,
                      THICKNESS = :thick,
                      STATUS = :status,
                      STAGE = '1'
                  WHERE ID_APP_TAG_EVENT = :coilpos";

            cmd.Parameters.Add("coilid", OracleDbType.Varchar2);
            cmd.Parameters.Add("width", OracleDbType.Decimal);
            cmd.Parameters.Add("thick", OracleDbType.Decimal);
            cmd.Parameters.Add("status", OracleDbType.Varchar2);
            cmd.Parameters.Add("coilpos", OracleDbType.Varchar2);

            for (int i = 0; i < dtTrack.Rows.Count; i++)
            {
                if (string.IsNullOrWhiteSpace(dtTrack.Rows[i][1].ToString()))
                {
                    if (i >= dtnewTrack.Rows.Count)
                        break;

                    DataRow dtrow = dtnewTrack.Rows[i];

                    cmd.Parameters["coilid"].Value = dtrow[1].ToString();

                    cmd.Parameters["width"].Value =
                        dtrow[2] == DBNull.Value ? (object)DBNull.Value : Convert.ToDecimal(dtrow[2]);

                    cmd.Parameters["thick"].Value =
                        dtrow[3] == DBNull.Value ? (object)DBNull.Value : Convert.ToDecimal(dtrow[3]);

                    cmd.Parameters["status"].Value = CoilPosition.ProcessMode;
                    cmd.Parameters["coilpos"].Value = dtrow[0].ToString();

                    success += cmd.ExecuteNonQuery();
                }
            }

            trans.Commit();
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error: " + ex.Message, "UpdateTrackingPage Error",
            MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
    finally
    {
        if (connection.State == ConnectionState.Open)
            connection.Close();
    }

    return success;
}