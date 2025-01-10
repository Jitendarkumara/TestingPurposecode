using System;
using System.Drawing;
using System.Drawing.Printing;
using System.Windows.Forms;

public class PrintExample : Form
{
    private Button printButton;

    public PrintExample()
    {
        printButton = new Button
        {
            Text = "Print 6x4 Page",
            Dock = DockStyle.Top
        };
        printButton.Click += PrintButton_Click;

        Controls.Add(printButton);
    }

    private void PrintButton_Click(object sender, EventArgs e)
    {
        PrintDocument printDocument = new PrintDocument();

        // Set the custom page size to 6x4 inches
        PaperSize paperSize = new PaperSize("Custom6x4", 600, 400); // 6x4 inches in hundredths of an inch
        printDocument.DefaultPageSettings.PaperSize = paperSize;

        printDocument.PrintPage += PrintDocument_PrintPage;

        try
        {
            printDocument.Print();
        }
        catch (Exception ex)
        {
            MessageBox.Show($"An error occurred while printing: {ex.Message}");
        }
    }

    private void PrintDocument_PrintPage(object sender, PrintPageEventArgs e)
    {
        // Draw something on the page
        string textToPrint = "This is a 6x4 inch page!";
        Font font = new Font("Arial", 16);
        PointF point = new PointF(50, 50);

        e.Graphics.DrawString(textToPrint, font, Brushes.Black, point);
    }

    [STAThread]
    static void Main()
    {
        Application.EnableVisualStyles();
        Application.Run(new PrintExample());
    }
}