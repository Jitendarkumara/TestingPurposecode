public class FeedersController : Controller
{
      public IActionResult FeedersChart(string mill, string from, string to,  string status)
  {

      // You can fetch submill data here based on the parameters
      var model = new FeedersChartViewModel
      {
          Mill = mill,
          FromDate = from,
          ToDate = to,
         
          Status = status,
          // You can also fetch the data here and pass it to the view
          // Data = _yourService.GetFeedersData(mill, from, to, shift, status)
      };

      return View(model);
  }
}
