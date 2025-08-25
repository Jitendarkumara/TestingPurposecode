public void millStartStop()
{
    if (Global.MillRunningSts)
    {
        // Attach once only
        SpinTimer.Tick -= TimerEventProcessor;
        SpinTimer.Tick += TimerEventProcessor;

        SpinTimer.Interval = 1;
        SpinTimer.Enabled = true;
        SpinTimer.Start();

        timer1.Start();

        RecoilTimer.Elapsed -= RecoilerTick;
        RecoilTimer.Elapsed += RecoilerTick;
        RecoilTimer.AutoReset = true;
        RecoilTimer.Enabled = true;
    }
    else
    {
        SpinTimer.Stop();
        timer1.Stop();

        // Detach handlers when stopping
        SpinTimer.Tick -= TimerEventProcessor;

        RecoilTimer.Elapsed -= RecoilerTick;
        RecoilTimer.Stop();
    }
}