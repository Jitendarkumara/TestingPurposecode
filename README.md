using System;
using System.Collections.Generic;
using System.Data;
using System.Linq;
using System.Web.Mvc;
using Newtonsoft.Json;

namespace YourNamespace.Controllers
{
    public class Sinter_PlantController : Controller
    {
        public JsonResult Furnace_Delay()
        {
            string sql = "SELECT Timestamp, SHIFT, FURNACE, TYPE, Reason AS DELAY, START_TIME, END_TIME FROM Demo.t_furnace_delays WHERE TRUNC(Timestamp) = TO_DATE('26-APR-2025', 'DD-MON-YYYY')";
            DataTable dt = DAL.GetRecords(sql);

            // Convert DataTable to List of Objects
            var delayList = dt.AsEnumerable().Select(row => new
            {
                SHIFT = row["SHIFT"]?.ToString(),
                FURNACE = row["FURNACE"]?.ToString(),
                TYPE = row["TYPE"]?.ToString(),
                DELAY = row["DELAY"]?.ToString(),
                START_TIME = row["START_TIME"]?.ToString(),
                END_TIME = row["END_TIME"]?.ToString()
            }).ToList();

            return Json(new { data = delayList }, JsonRequestBehavior.AllowGet);
        }
    }
}