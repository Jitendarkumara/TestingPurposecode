public void loadCMBdata()
{
    // MarkFlag
    cmbMarkFlag.Items.Clear();
    cmbMarkFlag.Items.Add("N");
    cmbMarkFlag.Items.Add("Y");
    cmbMarkFlag.SelectedIndex = 0;

    // MarkLogo
    var logoOptions = new List<KeyValuePair<string, string>>()
    {
        new KeyValuePair<string, string>("0", "No Logo"),
        new KeyValuePair<string, string>("1", "Option 2"),
        new KeyValuePair<string, string>("2", "Durashine"),
        new KeyValuePair<string, string>("3", "Colour Nova")
    };

    cmbMarkLogo.DataSource = logoOptions;
    cmbMarkLogo.DisplayMember = "Value";   // What user sees
    cmbMarkLogo.ValueMember = "Key";       // What you get in code
    cmbMarkLogo.SelectedIndex = 0;

    // DateTime flag
    cmbDateTime.Items.Clear();
    cmbDateTime.Items.Add("Y");
    cmbDateTime.Items.Add("N");
    cmbDateTime.SelectedIndex = 0;
}