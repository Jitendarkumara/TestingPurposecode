public IActionResult GetKWHData(string MillName, string feederName)
{
 

        MachineSectionDal M = new MachineSectionDal();

        List<Chartdata> Data = new List<Chartdata>();
        DataTable dt = new DataTable();
        dt = M.getChartdata(MillName, feederName);

        for (int i = 0; i < dt.Rows.Count; i++)
        {
            Chartdata chartdata = new Chartdata();
            chartdata.TimeStamp = Convert.ToDateTime(dt.Rows[i]["TimeStamp"].ToString());

            chartdata.Millname = (dt.Rows[i]["Mill_Id"].ToString());

            chartdata.KWH = (Convert.ToDouble(dt.Rows[i]["TotalActvalue"]));
            Data.Add(chartdata);
        }
        return Json(Data);
    
   
}

System.InvalidCastException
  HResult=0x80004002
  Message=Object cannot be cast from DBNull to other types.
  Source=System.Private.CoreLib
  StackTrace:
   at System.DBNull.System.IConvertible.ToDouble(IFormatProvider provider)
   at EmsApplication.Controllers.HomeController.GetKWHData(String MillName, String feederName) in D:\Jitu\EMS-Khopoli_Backup_17_03_25\EmsApplication\EmsApplication\Controllers\HomeController.cs:line 451
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncActionResultExecutor.Execute(ActionContext actionContext, IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeActionMethodAsync()
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeNextActionFilterAsync()
