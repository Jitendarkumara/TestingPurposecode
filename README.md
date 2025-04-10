private void button1_Click(object sender, EventArgs e)
{
    // Get values from controls
    string value1 = comboBox1.SelectedItem?.ToString() ?? "";
    string value2 = comboBox2.SelectedItem?.ToString() ?? "";
    string value3 = textBox1.Text;

    // Build CSV line
    string csvLine = $"{value1},{value2},{value3}";

    // Set CSV file path
    string filePath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.Desktop), "output.csv");

    // Check if file exists to add header if necessary
    bool fileExists = File.Exists(filePath);

    using (StreamWriter writer = new StreamWriter(filePath, append: true))
    {
        if (!fileExists)
        {
            writer.WriteLine("Combo1,Combo2,Text"); // header
        }
        writer.WriteLine(csvLine);
    }

    MessageBox.Show("Data saved to CSV!");
}