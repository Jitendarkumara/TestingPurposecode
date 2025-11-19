private Task<DataTable> PORSignalAsync()
{
    return Task.Run(() => PORSignal());
}