public void millStartStop()
{
    try
    {
        if (Global.MillRunningSts)
        {
            // Remove first to avoid multiple subscription
            SpinTimer.Tick -= TimerEventProcessor; 
            SpinTimer.Tick += TimerEventProcessor;

            if (!SpinTimer.Enabled)
                SpinTimer.Start();

            // other timers if needed
            // if (!timer1.Enabled) timer1.Start();
            // if (!RecoilTimer.Enabled) RecoilTimer.Start();
        }
        else
        {
            SpinTimer.Tick -= TimerEventProcessor;

            if (SpinTimer.Enabled)
                SpinTimer.Stop();

            if (timer1.Enabled)
                timer1.Stop();

            if (RecoilTimer.Enabled)
                RecoilTimer.Stop();
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error in millStartStop():\n" + ex.Message);
    }
}