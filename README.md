    private void UpdateRemainingDuration()
    {
        double totalDuration = double.TryParse(txtTotalduration.Text, out var result) ? result : 0;
        double usedDuration = panel2.Controls.OfType<Panel>()
            .Sum(panel => panel.Controls.OfType<TextBox>()
                .Where(tb => tb.ReadOnly) // Target duration boxes
                .Sum(tb => double.TryParse(tb.Text.Split(' ')[0], out var duration) ? duration : 0));

        txtRemainingDuration.Text = (totalDuration - usedDuration).ToString("0");
    }
