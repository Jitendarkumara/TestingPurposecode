public class ProductionController : Controller
{
    private readonly AppDbContext _context;

    public ProductionController(AppDbContext context)
    {
        _context = context;
    }

    public IActionResult Index()
    {
        var productionUnits = _context.ProductionUnits.ToList();
        return View(productionUnits);
    }
}