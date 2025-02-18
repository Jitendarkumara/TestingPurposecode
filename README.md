using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using YourNamespace.Models;

namespace YourNamespace.Controllers
{
    public class HomeController : Controller
    {
        public IActionResult Index()
        {
            var machines = new List<MachineData>
            {
                new MachineData { MachineName = "LDP Mill1", IsRunning = true, Speed = "1200 RPM", Size = "Large", Thickness = "5mm", Grade = "A", CustomerName = "Pims", Alert = "Low", KW = "500", KWh = "1000", KVAh = "1200" },
                new MachineData { MachineName = "LDP Mill2", IsRunning = false, Speed = "1100 RPM", Size = "Medium", Thickness = "4mm", Grade = "B", CustomerName = "ABC Corp", Alert = "High", KW = "450", KWh = "900", KVAh = "1100" },
                new MachineData { MachineName = "WCRM Mill-1", IsRunning = true, Speed = "1300 RPM", Size = "Small", Thickness = "3mm", Grade = "C", CustomerName = "XYZ Ltd", Alert = "Low", KW = "600", KWh = "1100", KVAh = "1300" },
                new MachineData { MachineName = "Power Plant", IsRunning = false, Speed = "1000 RPM", Size = "Large", Thickness = "6mm", Grade = "D", CustomerName = "PQR Industries", Alert = "High", KW = "400", KWh = "800", KVAh = "1000" },
            };

            return View(machines);
        }
    }
}
<body>
    <div class="container mt-4">
        <h2 class="text-center">Machine Data Table</h2>
        <table class="table table-bordered text-center">
            <thead>
                <tr>
                    <th>Parameter</th>
                    @foreach (var machine in Model)
                    {
                        <th>@machine.MachineName</th>
                    }
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>Mill Run/Stop</td>
                    @foreach (var machine in Model)
                    {
                        <td>
                            <span class="status-icon @(machine.IsRunning ? "green" : "red")"></span>
                            @(machine.IsRunning ? "Run" : "Stop")
                        </td>
                    }
                </tr>
                <tr>
                    <td>Speed</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.Speed</td>
                    }
                </tr>
                <tr>
                    <td>Size</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.Size</td>
                    }
                </tr>
                <tr>
                    <td>Thickness</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.Thickness</td>
                    }
                </tr>
                <tr>
                    <td>Grade</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.Grade</td>
                    }
                </tr>
                <tr>
                    <td>Customer Name</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.CustomerName</td>
                    }
                </tr>
                <tr>
                    <td>Alert</td>
                    @foreach (var machine in Model)
                    {
                        <td>
                            <span class="status-icon @(machine.Alert == "Low" ? "green" : "red")"></span>
                            @machine.Alert
                        </td>
                    }
                </tr>
                <tr>
                    <td>KW</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.KW</td>
                    }
                </tr>
                <tr>
                    <td>KWh</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.KWh</td>
                    }
                </tr>
                <tr>
                    <td>KVAh</td>
                    @foreach (var machine in Model)
                    {
                        <td>@machine.KVAh</td>
                    }
                </tr>
            </tbody>
        </table>
    </div>
</body>
</html>