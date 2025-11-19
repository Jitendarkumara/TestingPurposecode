 public void MillActualValue()
 {
     Db.DatabaseConnect();
     string query = "select  Entry_speed_act FROM T_PERIODIC_VALUE_LOG ORDER BY DATE_TIME DESC  fetch first 1 row only";
     OracleDataAdapter oda = new OracleDataAdapter(query, Db.Con);
     DataTable dtActual = new DataTable();
     oda.Fill(dtActual);
     if (dtActual.Rows.Count > 0)
     {
         GetProcessline();
         
         txtActual.Text = dtActual.Rows[0].ItemArray[0].ToString();
         //if (Convert.ToInt32(dtActual.Rows[0][0]) > 0)
         //{
         //    Global.MillRunningSts = true;
         //}
     }
     else
     {
        // Global.MillRunningSts = false;
         txtActual.Text = "0";
     }

 }
