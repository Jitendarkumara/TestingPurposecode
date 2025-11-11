if (btnLevel2 != null && !btnLevel2.IsDisposed)
{
    btnLevel2.Invoke(new Action(() =>
    {
        btnLevel2.BackColor = Color.Green;
        btnLevel2.Text = "LEVEL 2 : Connected";
    }));
}