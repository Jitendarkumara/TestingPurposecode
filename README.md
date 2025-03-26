private async void timer1_Tick(object sender, EventArgs e)
{
    try
    {
        await Task.Run(() => FetchDataFromDatabase());
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error: " + ex.Message);
    }
}

private void FetchDataFromDatabase()
{
    try
    {
        using (SqlConnection conn = new SqlConnection("your_connection_string"))
        {
            conn.Open();
            SqlCommand cmd = new SqlCommand("SELECT * FROM your_table", conn);
            SqlDataReader reader = cmd.ExecuteReader();
            // Process data...
        }
    }
    catch (SqlException ex)
    {
        // Log the error and prevent the system from hanging
        Console.WriteLine("Database error: " + ex.Message);
    }
}