public ActionResult Consumption(DateTime? date = null, string furnace = "C")
{
    var selectedDate = date ?? DateTime.Today.AddDays(-1); // yesterday
    var selectedFurnace = string.IsNullOrEmpty(furnace) ? "C" : furnace;

    var data = _repo.GetConsumptionData(selectedDate, selectedFurnace);
    ViewBag.SelectedDate = selectedDate;
    ViewBag.SelectedFurnace = selectedFurnace;

    return View(data);
}