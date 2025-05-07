private void dateTimePicker1_KeyUp(object sender, KeyEventArgs e)
{
    if (e.KeyCode == Keys.Up || e.KeyCode == Keys.Down)
    {
        // Move to the next field (hour -> minute -> AM/PM)
        SendKeys.Send("{RIGHT}");
    }
}