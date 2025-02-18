public IActionResult EmsDashboard()
{
    var productionUnits = new List<ProductionUnit>
    {
        new ProductionUnit { Id = 1, Name = "LDP Mill 1", IsRunning = true, IsHigh = false },
        new ProductionUnit { Id = 2, Name = "LDP Mill 2", IsRunning = false, IsHigh = true },
        new ProductionUnit { Id = 3, Name = "WCRM Mill 1", IsRunning = true, IsHigh = true },
        new ProductionUnit { Id = 4, Name = "Power Plant", IsRunning = false, IsHigh = false }
    };

    return View(productionUnits);
}