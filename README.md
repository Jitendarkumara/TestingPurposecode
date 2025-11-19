private async void TimerEventProcessor(object sender, EventArgs myEventArgs)
{
    try
    {
        // ---- Get DB values async ----
        var dtPORR = await PORSignalAsync();
        if (dtPORR == null || dtPORR.Rows.Count == 0)
            return;

        // ---- Update angles ----
        lbl_Timer.Text = (Convert.ToInt32(lbl_Timer.Text) + 1).ToString();

        rAngle = (rAngle + 5) % 360;
        lAngle = (lAngle - 5) % 360;

        string por1 = dtPORR.Rows[0][0].ToString();
        string por2 = dtPORR.Rows[0][1].ToString();

        // ---- Adjust POR logic ----
        if (por1 == "1")
        {
            circleMap[circlePOR1] = true;
            circleMap[circlePOR2] = false;
        }
        else if (por2 == "1")
        {
            circleMap[circlePOR2] = true;
            circleMap[circlePOR1] = false;
        }

        // ---- Single loop updates EVERYTHING ----
        foreach (var item in circleMap)
        {
            item.Key.angle = item.Value ? rAngle : lAngle;
            item.Key.Invalidate();
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show(ex.Message + "666");
    }
}