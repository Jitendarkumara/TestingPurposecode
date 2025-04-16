private void dgvPDO_SelectionChanged(object sender, EventArgs e)
{
    try
    {
        
        if (dgvPDO.SelectedRows.Count > 0)
        {
            PDOCoilNum = dgvPDO.SelectedRows[0].Cells[0].Value.ToString();
            //PDO.General Gp= new PDO.General();
            //PDO.Cleaning_Furnace Cl=new PDO.Cleaning_Furnace();
            //PDO.Galvanizing Gal = new PDO.Galvanizing();
            //PDO.Exit Ext=new PDO.Exit();
            if (btnGeneral.ForeColor == Color.Black)
            {
          
                //Gp.TopLevel = false;
                //Gp.AutoScroll = true;
                //pnlPdo.Controls.Add(Gp);
                //Gp.Show();

                PDO.General GP1 = (PDO.General)Application.OpenForms["General"];
                GP1.LoadGenInfoData();
            }
            }
            }
            }


            private void General_Load(object sender, EventArgs e)
{
    LoadGenInfoData();
}
public void LoadGenInfoData()
{
    try
    {
        comboBoxHoldReq.Items.Clear();
        comboBoxHoldReq.Items.Add("Y");
        comboBoxHoldReq.Items.Add("N");
        comboBoxHoldReq.Items.Add("P");
        comboBoxHoldReq.SelectedItem = "N";

        comboBoxProcStatus.Items.Clear();
        comboBoxProcStatus.Items.Add("P");
        comboBoxProcStatus.Items.Add("NP");
        comboBoxProcStatus.SelectedItem = "P";

        Db.DatabaseConnect();

        //string s = "SELECT * FROM T_COL_COIL_INFO_PDO where TCIP_PRODUCT_COIL='" + MasterPage.SelectedRow.Cells[0].Value.ToString() + "' ";
        //string s = "SELECT * FROM T_COL_COIL_INFO_PDO where TCIP_PRODUCT_COIL='" + MasterPage.CoilNum + "' ";
        string s = "SELECT CGD_ID_COIL,CGD_MK_CUSTOMER,CGD_TS_START,CGD_TS_END,CGD_WORK_DUR,CGD_TDC_NO,CGD_ID_COIL_MTHR,CGD_TR_NO,CGD_POR_NO,CGD_WIDTH,CGD_SEC1_COIL,CGD_SEC1_COIL_MIN,CGD_SEC1_COIL_MAX,ACT_WIDTH,CGD_ACT_THICKNESS,"+
                   " CGD_FL_SEAL_PAD,CGD_FL_MARK,CGD_FL_LOGO,CGD_FL_LABEL,CGD_MS_ACTL,CGD_MS_CAL,CGD_TR_WIND_MODE,CGD_NO_WELD,CGD_IDIA,CGD_ODIA,CGD_FL_SLEEVE,CGD_MS_SLEEVE,CGD_LN_COIL,CGD_TYP_SLEEVE," +
                   "CGD_TTC_AVG,CGD_TTC_TOT_DSAVG,CGD_TTC_TOT_CNAVG,CGD_TTC_TOT_WSAVG,CGD_BTC_AVG,CGD_BTC_TOT_WSAVG,CGD_BTC_TOT_CNAVG,CGD_BTC_TOT_DSAVG,CGD_COAT_TOT_WT,CGD_MEAS_MODE,CGD_COAT_HEAD_POS,"+
                   "CGD_CD_GRADE,CGD_CD_QLTY,CGD_CD_PROD,CGD_SUR_GRADE,CGD_SHAPE_GRADE,CGD_OFF_HEAD_LEN,CGD_OFF_TAIL_LEN,CGD_NEXT_PRC,CGD_HOLD_REQ,CGD_CD_RSN_HOLD,CGD_CD_RSN_HOLD1,CGD_CD_RSN_HOLD2,"+
                   " CGD_HOLD_OPR_ID from T_PDO_INFO where CGD_ID_COIL ='" + PDOMaster.PDOCoilNum + "' ";
        OracleCommand cmd = new OracleCommand(s, Db.Con);
        OracleDataReader dr = cmd.ExecuteReader();
        if (dr.Read())
        {
            txtDaughterCoil.Text = dr["CGD_ID_COIL"].ToString();
            txtCustomer.Text = dr["CGD_MK_CUSTOMER"].ToString();
            txtStartTime.Text = dr["CGD_TS_START"].ToString();
            txtEndTime.Text = dr["CGD_TS_END"].ToString();
            txtDuration.Text = dr["CGD_WORK_DUR"].ToString();
            txtTdc.Text = dr["CGD_TDC_NO"].ToString();                  
            txtMotherCoil.Text = dr["CGD_ID_COIL_MTHR"].ToString();
            txtTrNuumb.Text = dr["CGD_TR_NO"].ToString();
            txtUncoiller.Text = dr["CGD_POR_NO"].ToString();
           endtime = (DateTime)dr["CGD_TS_END"];
            if (dr["CGD_ACT_THICKNESS"] != DBNull.Value && Convert.ToDecimal(dr["CGD_ACT_THICKNESS"])>0)
            {
                txtActThk.Text= dr["CGD_ACT_THICKNESS"].ToString() ;

            }
            else
            {

                txtActWidth.Text = dr["CGD_SEC1_COIL"].ToString();
            }
            if (dr["ACT_WIDTH"] != DBNull.Value)
            {
                txtActWidth.Text = dr["ACT_WIDTH"].ToString();
            }
            else
            {
                txtActWidth.Text = dr["CGD_WIDTH"].ToString();
            }


            txtWidth.Text = dr["CGD_WIDTH"].ToString();
            txtThickness.Text = dr["CGD_SEC1_COIL"].ToString();
            txtMinThick.Text = dr["CGD_SEC1_COIL"].ToString();                                                                    //dr["CGD_SEC1_COIL_MIN"].ToString();
            txtMaxThick.Text = dr["CGD_SEC1_COIL"].ToString();                                                                                           //r["CGD_SEC1_COIL_MAX"].ToString();
            txtSealPadUsed.Text = dr["CGD_FL_SEAL_PAD"].ToString();
            txtMarkerUsed.Text = dr["CGD_FL_MARK"].ToString();
            txtLogoUsed.Text = dr["CGD_FL_LOGO"].ToString();
            txtLabelUsed.Text = dr["CGD_FL_LABEL"].ToString();
            txtMeasuredWt.Text = dr["CGD_MS_ACTL"].ToString();
            txtCalculatedWt.Text = dr["CGD_MS_ACTL"].ToString();                                                  //dr["CGD_MS_CAL"].ToString();
            txtWindingType.Text = dr["CGD_TR_WIND_MODE"].ToString();
            txtWeldPoint.Text = dr["CGD_NO_WELD"].ToString();
            txtInnerDia.Text = dr["CGD_IDIA"].ToString();
            txtOuterDia.Text = dr["CGD_ODIA"].ToString();
            txtSleeveUsed.Text = dr["CGD_FL_SLEEVE"].ToString();
            txtSleeveWt.Text = dr["CGD_MS_SLEEVE"].ToString();
            txtLength.Text = dr["CGD_LN_COIL"].ToString();
            txtSleeveType.Text = dr["CGD_TYP_SLEEVE"].ToString();

            txtAvgTOP.Text = dr["CGD_TTC_AVG"].ToString();
            txtAvgLeft.Text = dr["CGD_TTC_TOT_DSAVG"].ToString();
            txtAvgCenter.Text = dr["CGD_TTC_TOT_CNAVG"].ToString();
            txtAvgRight.Text = dr["CGD_TTC_TOT_WSAVG"].ToString();
            txtAvgBOT.Text = dr["CGD_BTC_AVG"].ToString();
            txtAvgLeftBOT.Text = dr["CGD_BTC_TOT_WSAVG"].ToString();
            txtAvgCenterBOT.Text = dr["CGD_BTC_TOT_CNAVG"].ToString();
            txtAvgRightBOT.Text = dr["CGD_BTC_TOT_DSAVG"].ToString();
            txtTOTCoatWT.Text = dr["CGD_COAT_TOT_WT"].ToString();
            txtMeasureMode.Text = dr["CGD_MEAS_MODE"].ToString();
            txtCoatGaugeHP.Text = dr["CGD_COAT_HEAD_POS"].ToString();
            //Quality
            txtSteelGrade.Text = dr["CGD_CD_GRADE"].ToString();
            txtQualitytCode.Text = dr["CGD_CD_QLTY"].ToString();
            txtProdCode.Text = dr["CGD_CD_PROD"].ToString();
            txtSurGrade.Text = dr["CGD_SUR_GRADE"].ToString();
            txtShapeGrade.Text = dr["CGD_SHAPE_GRADE"].ToString();

            txtHeadLength.Text = dr["CGD_OFF_HEAD_LEN"].ToString();
            txtTailLength.Text = dr["CGD_OFF_TAIL_LEN"].ToString();
            txtNextProcess.Text = dr["CGD_NEXT_PRC"].ToString();
            txtHoldType.Text = dr["CGD_HOLD_REQ"].ToString();

            if (dr["CGD_CD_RSN_HOLD"] != DBNull.Value)
            {
                txtHoldReason.Text = dr["CGD_CD_RSN_HOLD"].ToString();
            }
            else
            {
                txtHoldReason.Text = "NA";

            }
           
            txtHoldReason2.Text = dr["CGD_CD_RSN_HOLD1"].ToString();
            txtHoldReason3.Text = dr["CGD_CD_RSN_HOLD2"].ToString();
            txtHoldReason4.Text = dr["CGD_HOLD_OPR_ID"].ToString();
            
        }
        Db.ConClose();
    }
    catch (Exception ex)
    {
        MessageBox.Show(ex.ToString());
    }
}
