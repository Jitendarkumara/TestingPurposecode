using System;
using System.Drawing.Printing;

class Program
{
    static void Main()
    {
        // Get the default printer name
        PrinterSettings settings = new PrinterSettings();
        Console.WriteLine("Default Printer: " + settings.PrinterName);

        // Get all installed printers
        Console.WriteLine("All Available Printers:");
        foreach (string printer in PrinterSettings.InstalledPrinters)
        {
            Console.WriteLine(printer);
        }
    }
}