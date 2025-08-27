// Thread-safe UI update helper
private void SafeInvoke(Control control, Action action)
{
    if (control.InvokeRequired)
    {
        control.Invoke(new MethodInvoker(action));
    }
    else
    {
        action();
    }
}