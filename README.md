protected override void OnPaint(PaintEventArgs e)
{
    try
    {
        base.OnPaint(e);

        Rectangle rect = this.ClientRectangle;

        if (rect.Width > 0 && rect.Height > 0)
        {
            using (LinearGradientBrush brush = new LinearGradientBrush(
                rect, Color.White, Color.Blue, LinearGradientMode.Horizontal))
            {
                e.Graphics.FillRectangle(brush, rect);
            }
        }
    }
    catch (Exception ex)
    {
        Debug.WriteLine($"OnPaint Error: {ex.Message}");
        // Optionally log the error or display it in a less intrusive way
    }
}