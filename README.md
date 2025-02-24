foreach (string printer in System.Drawing.Printing.PrinterSettings.InstalledPrinters)
{
    Console.WriteLine(printer);
}