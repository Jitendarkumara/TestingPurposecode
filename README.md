private async void timerText_Tick(object sender, EventArgs e)
{
    if (Db.DatabaseConnectCheck())
    {
        await Task.Run(() =>
        {
            AutoProcessingPor();
            RecoilCoilId();
            AutoPor1ColorProcessig();
            AutoPor2ColorProcessig();
            MillActualValue();
            EtryExitAccumulator();
            FetchAllTextValues();
        });

        // UI-related work must be run on UI thread
        LoadTextBoxData();
    }
}