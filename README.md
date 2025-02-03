    public WIP_Form wIP_Form;
//  WIP_Form wIP_Form = new WIP_Form();
  if (wIP_Form != null)
  {

      wIP_Form.Close();
  }
        
  Db.DatabaseConnect();
  string s = "SELECT TCIP_INPUT_COIL, TCIP_CIL_END_TIME FROM T_COL_COIL_INFO_PDO WHERE TCIP_PRODUCT_COIL='" + Lbl_coil.Text + "'";
  OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
  DataTable Dtcoil = new DataTable();
  da.Fill(Dtcoil);

  DateTime endtime = (DateTime)Dtcoil.Rows[0]["TCIP_CIL_END_TIME"];

  string shift1;
  TimeSpan shift = endtime.TimeOfDay;
  if (shift >= new TimeSpan(6, 0, 0) && shift < new TimeSpan(14, 0, 0))
  {
      shift1 = "A";

  }
  else if (shift >= new TimeSpan(14, 0, 0) && shift < new TimeSpan(22, 0, 0))
  {
      shift1 = "B";
  }
  else
  {
      shift1 = "C";
  }

  BatchDetail.BatchNumber = Dtcoil.Rows[0]["TCIP_INPUT_COIL"].ToString();
  BatchDetail.shift = shift1;
  BatchDetail.Bdate = DateTime.Now.ToString();

  WIP_Form wIP_Form = new WIP_Form();

  wIP_Form.ShowDialog();
