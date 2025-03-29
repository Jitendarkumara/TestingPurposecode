public void ConClose()
{
    if (Con != null && Con.State == ConnectionState.Open)
    {
        Con.Close();
        Console.WriteLine("Database connection closed.");
    }

    if (Con != null)
    {
        Con.Dispose();
        Con = null; // Prevent using disposed object
    }
}