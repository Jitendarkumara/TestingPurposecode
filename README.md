      public int UpdateTrackingPage(DataTable dtnewTrack)
      {
          DataTable dtTrack = new DataTable();
          dtTrack = GetlineData();
          int success = 0;
          OracleCommand cmd1 = new OracleCommand();
          cmd1.CommandType = CommandType.Text;
          cmd1.Connection = connection;
          for(int i = 0; i < dtTrack.Rows.Count;i++)
          {
              if (dtTrack.Rows[i][1].ToString() == "")
              {
                  if (i > 0)
                      //i = 4;
                      i = 1;
                  DataRow dtrow = dtnewTrack.Rows[i];
                  string coilPos = dtrow[0].ToString();
                  string coilid = dtrow[1].ToString();
                  object coilwidth = null;
                  object coilthick = null;
                  if (dtrow[2] != DBNull.Value)
                  {
                      coilwidth = Convert.ToDecimal(dtrow[2]);
                  }
                  else
                  {
                      coilwidth = "null";
                  }

                  if (dtrow[3] != DBNull.Value)
                  {
                      coilthick = Convert.ToDecimal(dtrow[3]);
                  }
                  else
                  {
                      coilthick = "null";
                  }

                  if (connection.State == ConnectionState.Open)
                  {
                      connection.Close();
                      connection.Open();
                  }
                  else
                  {
                      connection.Open();
                  }

                  OracleTransaction trans = connection.BeginTransaction();
                  cmd1.Transaction = trans;
                  cmd1.CommandText = "update T_GP2_TRACKING_PAGE set COIL_ID ='" + coilid + "',WIDTH =" + coilwidth + ",THICKNESS =" + coilthick + ",STATUS = '" + CoilPosition.ProcessMode + "',STAGE = '1' where ID_APP_TAG_EVENT = '" + coilPos + "'";

                  try
                  {
                      success += cmd1.ExecuteNonQuery();
                      //success += 1;
                      if (success > 0)
                      {
                          trans.Commit();
                      }
                      else
                      {
                          trans.Rollback();
                          MessageBox.Show("Error Occured while updating " + coilid + " in database");
                          //return 0;
                      }
                  }
                  catch (Exception ex)
                  {
                      MessageBox.Show(ex.Message);
                  }
              }
              else
              {
                  //success += 3;
                  success += 0;
              }
              
          }
          
          return success;
      }
