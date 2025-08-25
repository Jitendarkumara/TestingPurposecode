  public void millStartStop()
  {
      
      if (Global.MillRunningSts)
      {
        
          SpinTimer.Tick += new EventHandler(TimerEventProcessor);
          SpinTimer.Interval = 1;
          SpinTimer.Enabled = true;
          SpinTimer.Start();
          timer1.Start();
          RecoilTimer.Elapsed += RecoilerTick;
          RecoilTimer.AutoReset = true; ////
          RecoilTimer.Enabled = true;

      }
      else
      {
          SpinTimer.Tick -= new EventHandler(TimerEventProcessor);
          SpinTimer.Stop();
          timer1.Stop();
          RecoilTimer.Elapsed += RecoilerTick;
          RecoilTimer.AutoReset = true; ////
          RecoilTimer.Enabled = true;
      }

  }
      
