private void textBox1_Enter(object sender, EventArgs e)
{
    if (textBox1.Text == "Enter your name")
    {
        textBox1.Text = "";
        textBox1.ForeColor = Color.Black;
    }
}

private void textBox1_Leave(object sender, EventArgs e)
{
    if (string.IsNullOrWhiteSpace(textBox1.Text))
    {
        textBox1.Text = "Enter your name";
        textBox1.ForeColor = Color.Gray;
    }
}