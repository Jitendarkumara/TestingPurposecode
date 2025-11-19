public void MillActualValue()
{
    try
    {
        Db.DatabaseConnect();

        if (Db.Con.State != ConnectionState.Open)
            Db.Con.Open();

        string query = @"
            SELECT Entry_speed_act 
            FROM T_PERIODIC_VALUE_LOG 
            ORDER BY DATE_TIME DESC 
            FETCH FIRST 1 ROW ONLY";

        DataTable dtActual = new DataTable();

        using (OracleDataAdapter oda = new OracleDataAdapter(query, Db.Con))
        {
            oda.Fill(dtActual);
        }

        if (dtActual.Rows.Count > 0)
        {
            GetProcessline();   // Only if needed

            object val = dtActual.Rows[0][0];
            txtActual.Text = val != DBNull.Value ? val.ToString() : "0";
        }
        else
        {
            txtActual.Text = "0";
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error loading mill actual value: " + ex.Message,
                        "MillActualValue Error",
                        MessageBoxButtons.OK,
                        MessageBoxIcon.Error);
    }
    finally
    {
        if (Db.Con.State == ConnectionState.Open)
            Db.Con.Close();
    }
}