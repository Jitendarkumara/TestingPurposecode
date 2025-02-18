The image appears to be an Excel sheet displaying a tabular representation of different mills, plants, and production units with status indicators (Run/Stop and Low/High) using red and green dots.

To create a similar view in ASP.NET Core using Entity Framework, follow these steps:

1. Set Up the Database Model (Entity Framework)

Create a model to represent the production unit statuses.

public class ProductionUnit
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsRunning { get; set; } // True = Running, False = Stopped
    public bool IsHigh { get; set; } // True = High, False = Low
}

2. Create the Database Context

Add a DbContext to manage the database.

public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }

    public DbSet<ProductionUnit> ProductionUnits { get; set; }
}

3. Seed Sample Data (Optional)

In Program.cs or via a migration, seed data like:

using (var scope = app.Services.CreateScope())
{
    var db = scope.ServiceProvider.GetRequiredService<AppDbContext>();
    db.Database.EnsureCreated();

    if (!db.ProductionUnits.Any())
    {
        db.ProductionUnits.AddRange(
            new ProductionUnit { Name = "LDP Mill 1", IsRunning = true, IsHigh = false },
            new ProductionUnit { Name = "LDP Mill 2", IsRunning = false, IsHigh = true },
            new ProductionUnit { Name = "WCRM Mill 1", IsRunning = true, IsHigh = true }
        );
        db.SaveChanges();
    }
}

4. Create the Controller

Generate a controller to fetch data.

[Route("api/production")]
[ApiController]
public class ProductionController : ControllerBase
{
    private readonly AppDbContext _context;

    public ProductionController(AppDbContext context)
    {
        _context = context;
    }

    [HttpGet]
    public async Task<IActionResult> GetProductionUnits()
    {
        return Ok(await _context.ProductionUnits.ToListAsync());
    }
}

5. Create the Razor Page or View (Frontend)

Use Razor Pages or an MVC View to display the table with colored indicators.

Razor View (Index.cshtml)

@model List<ProductionUnit>

<table class="table table-bordered">
    <thead>
        <tr>
            <th>Unit Name</th>
            <th>Run</th>
            <th>Stop</th>
            <th>Low</th>
            <th>High</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var unit in Model)
        {
            <tr>
                <td>@unit.Name</td>
                <td><span class="dot @(unit.IsRunning ? "green" : "red")"></span></td>
                <td><span class="dot @(!unit.IsRunning ? "green" : "red")"></span></td>
                <td><span class="dot @(!unit.IsHigh ? "green" : "red")"></span></td>
                <td><span class="dot @(unit.IsHigh ? "green" : "red")"></span></td>
            </tr>
        }
    </tbody>
</table>

<style>
    .dot {
        height: 15px;
        width: 15px;
        border-radius: 50%;
        display: inline-block;
    }
    .green { background-color: green; }
    .red { background-color: red; }
</style>

6. Run the Application

Start the ASP.NET Core app (dotnet run).

Navigate to the Razor page (/Home/Index).

The table will display green and red dots based on machine statuses.


This will create a web-based version of your Excel sheet using ASP.NET Core and Entity Framework. Let me know if you need further customizations!

