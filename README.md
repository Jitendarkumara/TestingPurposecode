comboBox1.DisplayMember = "Text";
comboBox1.ValueMember = "Value";
comboBox1.DataSource = new List<KeyValuePair<string, string>>()
{
    new KeyValuePair<string, string>("1", "Option 1"),
    new KeyValuePair<string, string>("2", "Option 2")
};

string selectedText = comboBox1.Text;
string selectedValue = ((KeyValuePair<string, string>)comboBox1.SelectedItem).Key;