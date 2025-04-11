public void RecoilCoilId()
{
    try
    {
        db.DatabaseConnect();

        string query = "SELECT COIL_ID FROM T_EVENT_TRACKING WHERE ID_APP_TAG_EVENT = 'RECOILER'";
        using (OracleDataAdapter da = new OracleDataAdapter(query, conn))
        {
            DataTable dtRecoiler = new DataTable();
            da.Fill(dtRecoiler);

            if (dtRecoiler.Rows.Count > 0)
            {
                object coilId = dtRecoiler.Rows[0]["COIL_ID"];
                txtRecoiler2.Text = coilId != DBNull.Value ? coilId.ToString() : string.Empty;
            }
            else
            {
                txtRecoiler2.Text = string.Empty;
            }
        }
    }
    catch (Exception ex)
    {
        // Consider logging the error here for debugging
        // MessageBox.Show(ex.Message); 
    }
    finally
    {
        db.ConClose(); // Ensure connection closes even if there's an error
    }
}