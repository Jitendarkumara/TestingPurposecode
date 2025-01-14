  PrintDocument printDocument = new PrintDocument();
  PaperSize Psize = new PaperSize("A6", 583, 413);
  printDocument.PrinterSettings.PrinterName = "Datamax DMX 400";
 printDocument.PrintPage += (sender, e) =>
  {
      Bitmap bmp = new Bitmap(tableLayoutPanel1.Width, tableLayoutPanel1.Height, System.Drawing.Imaging.PixelFormat.Format64bppPArgb);
      bmp.SetResolution(600, 600);
      // tableLayoutPanel1.DrawToBitmap(bmp, new Rectangle(0, 0, bmp.Width, bmp.Height));
       tableLayoutPanel1.DrawToBitmap(bmp, new Rectangle(0, 0, 583, 413));
  int x = (e.PageBounds.Width - bmp.Width) / 2;
      int y = (e.PageBounds.Height - bmp.Height) / 2;
       Rectangle PrintArea = new Rectangle(20, 20,666,474);
 e.Graphics.DrawImage(bmp, PrintArea);

  };
