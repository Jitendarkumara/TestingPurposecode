public ActionResult Consumption(DateTime? date=DateTime.Now-1, string furnace="C")
            {
                var selectedDate = date ?? DateTime.Today;
                var selectedFurnace = string.IsNullOrEmpty(furnace) ? "C" : furnace;

                var data = _repo.GetConsumptionData(selectedDate, selectedFurnace);
                ViewBag.SelectedDate = selectedDate;
                ViewBag.SelectedFurnace = selectedFurnace;

                return View(data);
            }
        
