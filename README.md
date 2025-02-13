using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using MonitoringDashboard.Models;
using System.Linq;
using System.Threading.Tasks;

namespace MonitoringDashboard.Controllers
{
    public class SensorController : Controller
    {
        private readonly MonitoringDbContext _context;

        public SensorController(MonitoringDbContext context)
        {
            _context = context;
        }

        public async Task<IActionResult> Index()
        {
            var data = await _context.SensorDatas.OrderBy(s => s.Timestamp).ToListAsync();
            return View(data);
        }
    }
}