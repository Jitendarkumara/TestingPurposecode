  public DataTable GetActiveCoilEvents()
  {
      DataTable dtEventTble = new DataTable();
      OracleDataAdapter Evtda = new OracleDataAdapter("select ID_APP_TAG_EVENT,COIL_ID,WIDTH,THICKNESS,P_MODE from T_EVENT_TRACKING", connection);
      if (connection.State == ConnectionState.Open)
      {
          connection.Close();
          connection.Open();
      }
      else
      {
          connection.Open();
      }
      Evtda.Fill(dtEventTble);

      if (dtEventTble != null)
      {
          if (dtEventTble.Rows[0][1] != DBNull.Value)
          {
              CoilPosition.L1_APP_POR_SELECTION = dtEventTble.Rows[0][1].ToString();
              CoilPosition.ProcessMode = dtEventTble.Rows[0][4].ToString();
          }
          else
          {
              CoilPosition.L1_APP_POR_SELECTION = "";
          }
          if (dtEventTble.Rows[1][1] != DBNull.Value)
          {
              CoilPosition.L1_WELD_BR2 = dtEventTble.Rows[1][1].ToString();
          }
          else
          {
              CoilPosition.L1_WELD_BR2 = "";
          }
          if (dtEventTble.Rows[2][1] != DBNull.Value)
          {
              CoilPosition.L1_WELD_BR3 = dtEventTble.Rows[2][1].ToString();
          }
          else
          {
              CoilPosition.L1_WELD_BR3 = "";
          }
          if (dtEventTble.Rows[3][1] != DBNull.Value)
          {
              CoilPosition.L1_WELD_BR8 = dtEventTble.Rows[3][1].ToString();
          }
          else
          {
              CoilPosition.L1_WELD_BR8 = "";
          }

          if (dtEventTble.Rows[4][1] != DBNull.Value)
          {
              CoilPosition.L1_COIL_EXT = dtEventTble.Rows[4][1].ToString();
          }
          else
          {
              CoilPosition.L1_COIL_EXT = "";
          }
          if(CoilPosition.tblActiveCoils != null)
          {
              CoilPosition.tblActiveCoils.Clear();
          }
          CoilPosition.tblActiveCoils = dtEventTble.AsEnumerable().GroupBy(x => !string.IsNullOrWhiteSpace(x.Field<string>("COIL_ID"))).Select(x => x.First()).CopyToDataTable();
          return dtEventTble;
      }
      else
      {
          return null;
      }
      
  }
