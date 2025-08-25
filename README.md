public void millStartStop()
{
    if (Global.MillRunningSts)
    {
        // Start only if not already running
        if (!SpinTimer.Enabled)
        {
            SpinTimer.Interval = 1;
            SpinTimer.Start();
        }

        if (!timer1.Enabled)
        {
            timer1.Start();
        }

        if (!RecoilTimer.Enabled)
        {
            RecoilTimer.AutoReset = true;
            RecoilTimer.Start();
        }
    }
    else
    {
        // Stop only if running
        if (SpinTimer.Enabled)
            SpinTimer.Stop();

        if (timer1.Enabled)
            timer1.Stop();

        if (RecoilTimer.Enabled)
            RecoilTimer.Stop();
    }
}