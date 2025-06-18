private void UpdateRemainingDuration()
{
    double totalDuration = double.TryParse(txtTotalduration.Text, out var result) ? result : 0;

    double usedDuration = 0;

    // Go through each inner Panel inside panel2
    foreach (Panel subPanel in panel2.Controls.OfType<Panel>())
    {
        // Look for the TextBox with Name = "txtDuration"
        TextBox durationBox = subPanel.Controls
            .OfType<TextBox>()
            .FirstOrDefault(tb => tb.Name == "txtDuration");

        if (durationBox != null && double.TryParse(durationBox.Text, out double dur))
        {
            usedDuration += dur;
        }
    }

    double remaining = totalDuration - usedDuration;
    txtRemainingDuration.Text = remaining.ToString("0");
}