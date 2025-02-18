public class ProductionUnit
{
  
    public int ?Id { get; set; }
    public string ?Name { get; set; }
    public bool IsRunning { get; set; } // True = Running, False = Stopped
    public bool IsHigh { get; set; } // True = High, False = Low
}

 public IActionResult EmsDashboard()
 {
    // var productionUnits = _context.ProductionUnits.ToList();
     return View(productionUnits);
 }
