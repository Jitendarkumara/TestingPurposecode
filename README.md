  private DataTable PORSignal()
  {

      try
      {
          DBContext evtDbContext = new DBContext();
          DataTable dtPOR = new DataTable();
          dtPOR = evtDbContext.GetPOR();
          return dtPOR;
      }
      catch
      {
          return null;

      }
  }

   private void TimerEventProcessor(Object obj, EventArgs myEventArgs)
 {
     DataTable dtPORR = new DataTable();
     
     //try
     //{         
       dtPORR= PORSignal();
       }
       }


        public DataTable GetPOR()
 {
     DataTable dtPOR = new DataTable();
     try
     {
       //  string Query = "select CGP_ID_COIL,ID_APP_TAG from t_event_log where date_time =(select max(date_time) from t_event_log where ID_APP_TAG in('L1_POR1_SEL','L1_POR2_SEL'))";
         string Query = "select L1_POR1_SEL,L1_POR2_SEL from V_LATEST_PERIODIC_VALUE";
         using (OracleDataAdapter oda = new OracleDataAdapter(Query, connection))
         {
             if (connection.State == ConnectionState.Closed)
             {
                 connection.Open();
             }
             oda.Fill(dtPOR);
         }
        
         dtPOR.AcceptChanges();
         return dtPOR;
     }
     catch (OracleException oex)
     {
         // Handle Oracle-specific exceptions
         Console.WriteLine(oex.Message);
         return null;
     }
     catch (Exception ex)
     {
         // Handle other exceptions
         Console.WriteLine(ex.Message);
         return null;
     }
     finally
     {
         if (connection.State == ConnectionState.Open)
         {
             connection.Close();
         }
     }
 }
