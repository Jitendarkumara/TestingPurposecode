var timer = new System.Windows.Forms.Timer();
timer.Interval = 1; // 1 millisecond
timer.Tick += (s, e) => {
    // Your fast code here
};
timer.Start();