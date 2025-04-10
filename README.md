private void button1_Click(object sender, EventArgs e)
{
    // Get values from controls
    string value1 = comboBox1.SelectedItem?.ToString() ?? "";
    string value2 = comboBox2.SelectedItem?.ToString() ?? "";
    string value3 = textBox1.Text;

    // Prepare CSV content
    List<string> csvLines = new List<string>
    {
        "Combo1,Combo2,Text", // header
        $"{value1},{value2},{value3}"
    };

    // Set file path (e.g., Desktop)
    string filePath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.Desktop), "output.csv");

    // Write the file (overwrites if exists)
    File.WriteAllLines(filePath, csvLines);

    MessageBox.Show("CSV file created and data written (overwritten if existed).");
}