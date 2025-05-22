using CCL.OPC_Client;
using DocumentFormat.OpenXml.Math;
using iTextSharp.text.xml.xmp;
using Oracle.ManagedDataAccess.Client;
using Org.BouncyCastle.Crypto.Agreement;
using PDO.Custom_Control;
using PDO.Main_Class;
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Media;
using System.Reflection.Emit;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using System.Timers;

namespace PDO.Back_Up
{
    public partial class Home : Form
    {
        DBhelper Db = new DBhelper();
        OPC_Client OPC = new OPC_Client(); 
        float RightAngle = 0;
        float LeftAngle = 0;
        int count = 1;
        int EndPor1Count = 1;
        SoundPlayer player = new SoundPlayer();
        DateTime dat;
        public Home()
        {
            InitializeComponent();
            if (Db.DatabaseConnectCheck())
            {
                lOADdata();
            }

        }
        private void Home_Load(object sender, EventArgs e)
        {
            try
            {
                if (Db.DatabaseConnectCheck())
                {
                    pnlExtraModified.Visible = false;
                    //For Slow Processing accumulaor value in timer_text Event
                    timerEntry.Stop();

                    LoadTextBoxData();
                    // ShowCoilImg();
                    //Timer for colour
                    lblresult.Visible = false;
                    lblEndPor1.Visible = false;
                    //ExitAccumulator
                    //timerExit.Start();
                    //timerExit.Interval = 5000;
                    progressBarExit.Visible = false;
                    progressBarEntry.Visible = false;
                    //New Requirement
                    LocationInvisible();
                    AutoProcessingPor();
                    //stop

                    EndPor1Timer.Stop();


                     
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        //After Application Crash, when application on Compare E_event_Tracking Table
        public void AutoProcessingPor()
        {
            try
            {
                Db.DatabaseConnect();
                string query = "select COIL_ID FROM T_EVENT_TRACKING WHERE ID_APP_TAG_EVENT='UNC_SELECTED'";
                OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
                DataTable dt = new DataTable();
                da.Fill(dt);
                Db.ConClose();
                if (dt.Rows.Count > 0)
                {
                    if (textBox17.Text != String.Empty)
                    {
                        if (dt.Rows[0].ItemArray[0].ToString() == textBox17.Text)
                        {

                            btnPor1.BackColor = Color.Red;
                            btnPor1.Text = "Processing";
                            btnPor1End.Enabled = false;

                            btnPor2.BackColor = Color.FromArgb(59, 89, 152);
                            btnPor2.Enabled = false;
                            btnEnd.Enabled = false;
                            //tRACKING EVENT
                            //UpdTrackEventPOR1();

                           // Por1timer.Start();

                            timerEntry.Start();
                            //timerColor.Start();
                            dat = DateTime.Now;
                        }
                        else if(dt.Rows[0].ItemArray[0].ToString() != textBox17.Text)
                        {
                           // Por1timer.Stop();   // commented by jitu on 05-FEB-2025
                            btnPor1.BackColor = Color.FromArgb(59, 89, 152);
                            btnPor1.Text = "START";
                        }
                    }
                   else if (textBox16.Text != String.Empty)
                    {
                        if (dt.Rows[0].ItemArray[0].ToString() == textBox16.Text)
                        {

                            btnPor2.BackColor = Color.Red;
                            btnPor2.Text = "Processing";
                            btnEnd.Enabled = false;

                            btnPor1.BackColor = Color.FromArgb(59, 89, 152);
                            btnPor1.Enabled = false;
                            btnPor1End.Enabled = false;
                            Por2timer.Start();
                            //Tracking Event
                            // UpdTrackEventPOR2();

                            timerEntry.Start();
                            
                            dat = DateTime.Now;
                        }
                        else if (dt.Rows[0].ItemArray[0].ToString() != textBox16.Text)
                        {
                            Por2timer.Stop();
                            btnPor2.BackColor = Color.FromArgb(59, 89, 152);
                            btnPor2.Text = "START";
                        }
                    }
                }
                Db.ConClose();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        public void LocationInvisible()
        {
            txtPor1c1.Visible = false;
            txtPor1c2.Visible = false;
            txtPor1c3.Visible = false;
            txtPor2c1.Visible = false;
            txtPor2c2.Visible = false;
            txtPor2c3.Visible = false;
            PictPor1c3S.Visible = false;
            PictPor1c3.Visible=false;
            PictPor1c2S.Visible = false;
            PictPor1c2.Visible = false;
            PictPor1c1S.Visible = false;
            PictPor1c1.Visible = false;
            PictPor2c3S.Visible = false;
            PictPor2c3.Visible = false;
            PictPor2c2S.Visible = false;
            PictPor2c2.Visible = false;
            PictPor2c1S.Visible = false;
            PictPor2c1.Visible = false;
            lblpor1C3.Visible = false;
            lblpor1C2.Visible = false;
            lblpor1C1.Visible = false;
            lblpor2C3.Visible = false;
            lblpor2C2.Visible = false;
            lblpor2C1.Visible = false;

        }
        //public void ShowCoilImg()
        //{
        //    try
        //    {
        //        if (txtPor1c3.Text != string.Empty)
        //        {
        //            PictPor1c3.Visible = true;
        //            PictPor1c3S.Visible = false;

        //        }
        //        if (txtPor1c2.Text != string.Empty)
        //        {
        //            PictPor1c2.Visible = true;
        //            PictPor1c2S.Visible = false;

        //        }
        //        if (txtPor1c1.Text != string.Empty)
        //        {
        //            PictPor1c1.Visible = true;
        //            PictPor1c1S.Visible = false;

        //        }
        //        if (txtPor2c3.Text != string.Empty)
        //        {
        //            PictPor2c3.Visible = true;
        //            PictPor2c3S.Visible = false;

        //        }
        //        if (txtPor2c2.Text != string.Empty)
        //        {
        //            PictPor2c2.Visible = true;
        //            PictPor2c2S.Visible = false;

        //        }
        //        if (txtPor2c1.Text != string.Empty)
        //        {
        //            PictPor2c1.Visible = true;
        //            PictPor2c1S.Visible = false;

        //        }
        //    }
        //    catch (Exception ex)
        //    {
        //        MessageBox.Show(ex.Message);
        //    }
        //}
        public void Rotate()
        {
            try
            {
                if (txtPor1.Text != String.Empty)
                {
                    POR1Rotation();

                }
                //else if (txtPor2.Text != String.Empty)
                //{
                //    POR2Rotation();
                //}
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }

        }

        //por1 and por2 value
        //public void LoadTextBoxData()
        //{
        //    try
        //    {
        //        Db.DatabaseConnect();
        //        string s = "select COIL_ID,LOC from T_COIL_LOC";
        //        OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
        //        DataTable dtText = new DataTable();
        //        da.Fill(dtText);
        //        Db.ConClose();
        //        if (dtText.Rows.Count > 0)
        //        {

        //            //if (dtText.Rows[0]["COIL_ID"].ToString() != null)
        //            //{
        //            //    txtPor1.Text = dtText.Rows[0]["COIL_ID"].ToString();

        //            //}

        //            //if (dtText.Rows[4]["COIL_ID"].ToString() != null)
        //            //{
        //            //    txtPor2.Text = dtText.Rows[4]["COIL_ID"].ToString();

        //            //}
        //            if (dtText.Rows[0][0] != DBNull.Value && dtText.Rows[1]["LOC"].ToString() == "POR1")
        //            {
        //                textBox17.Text = dtText.Rows[0][0].ToString();
        //            }
        //            if (dtText.Rows[1][0] != DBNull.Value &&  dtText.Rows[0]["LOC"].ToString() == "POR2")
        //            {
        //                textBox16.Text = dtText.Rows[1][0].ToString();
        //            }
        //            if (dtText.Rows[0][0] == DBNull.Value)
        //            {
        //                textBox17.Text = "";
        //            }
        //            if (dtText.Rows[1][0] == DBNull.Value)
        //            {
        //                textBox16.Text = "" ;
        //            }
        //            else
        //            {

        //            }
        //        }
        //    }
        //    catch (Exception ex)
        //    {
        //        ////MessageBox.Show(ex.Message);
        //    }
        //}

        public void LoadTextBoxData()
        {
            try
            {
                Db.DatabaseConnect();
                string s = "SELECT COIL_ID, LOC FROM T_COIL_LOC";
                OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
                DataTable dtText = new DataTable();
                da.Fill(dtText);
                Db.ConClose();

                // Clear text boxes initially
                textBox17.Text = "";
                textBox16.Text = "";

                // Loop through the rows and assign correct values based on LOC
                foreach (DataRow row in dtText.Rows)
                {
                    if (row["LOC"].ToString() == "POR1")
                    {
                        textBox17.Text = row["COIL_ID"].ToString();
                    }
                    else if (row["LOC"].ToString() == "POR2")
                    {
                        textBox16.Text = row["COIL_ID"].ToString();
                    }
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error: " + ex.Message, "Database Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
        }




        public void lOADdata()
        {
            try
            {
                Db.DatabaseConnect();
                string s = "select VAL FROM T_COL_MAN_TRACK ";
                OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
                DataTable dt = new DataTable();
                da.Fill(dt);
                Db.ConClose();
                if (dt.Rows.Count > 0)
                {                   
                    if (dt.Rows[6].ItemArray[0].ToString() != null)
                    {
                        txtPor1.Visible = true;
                        PictPor1c3.Visible = true;
                        txtPor1.Text = dt.Rows[6].ItemArray[0].ToString();
                    }
                    if (dt.Rows[7].ItemArray[0].ToString() != null)
                    {
                        txtPor2.Visible = true;
                        PictPor1c3.Visible = true;
                        txtPor2.Text = dt.Rows[7].ItemArray[0].ToString();
                    }
                    
                    else
                    {
                        //TextInvisible();
                    }
                }
                else
                {
                    // cmbPor1c2.Text = "select";
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        public void POR1Rotation()
        {
            try
            {


                RightAngle += 5;
                LeftAngle -= 5;
                if (RightAngle >= 360)
                    RightAngle = 0;

                if (LeftAngle <= -360)
                    LeftAngle = 0;



                circle92.Invalidate();
                circle92.angle = LeftAngle;

                circle93.Invalidate();
                circle93.angle = RightAngle;

                circle94.Invalidate();
                circle94.angle = LeftAngle;

                circle96.Invalidate();
                circle96.angle = RightAngle;

                //Cir5.Invalidate();
                // Cir5.angle = RightAngle;


                circle97.Invalidate();
                circle97.angle = RightAngle;

                circle98.Invalidate();
                circle98.angle = LeftAngle;

                circle99.Invalidate();
                circle99.angle = LeftAngle;

                circle101.Invalidate();
                circle101.angle = LeftAngle;

                circle103.Invalidate();
                circle103.angle = LeftAngle;

                //  circle8.Invalidate();
                //  circle8.angle = LeftAngle;

                circle104.Invalidate();
                circle104.angle = LeftAngle;

                circle107.Invalidate();
                circle107.angle = LeftAngle;

                circle108.Invalidate();
                circle108.angle = LeftAngle;

                circle118.Invalidate();
                circle118.angle = LeftAngle;

                circle119.Invalidate();
                circle119.angle = LeftAngle;

                circle120.Invalidate();
                circle120.angle = LeftAngle;

                circle121.Invalidate();
                circle121.angle = LeftAngle;

                circle122.Invalidate();
                circle122.angle = LeftAngle;

                circle125.Invalidate();
                circle125.angle = LeftAngle;


                circle126.Invalidate();
                circle126.angle = LeftAngle;

                circle127.Invalidate();
                circle127.angle = RightAngle;

                circle128.Invalidate();
                circle128.angle = LeftAngle;

                circle129.Invalidate();
                circle129.angle = RightAngle;

                circle130.Invalidate();
                circle130.angle = RightAngle;

                circle131.Invalidate();
                circle131.angle = LeftAngle;

                circle45.Invalidate();
                circle45.angle = LeftAngle;

                //  circle21.Invalidate();
                //  circle21.angle = LeftAngle;

                circle44.Invalidate();
                circle44.angle = LeftAngle;


                /// ----------------------------------------------
                circle88.Invalidate();
                circle88.angle = LeftAngle;

                circle89.Invalidate();
                circle89.angle = LeftAngle;

                circle105.Invalidate();
                circle105.angle = RightAngle;

                circle106.Invalidate();
                circle106.angle = RightAngle;

                // circle25.Invalidate();
                // circle25.angle = LeftAngle;

                circle115.Invalidate();
                circle115.angle = LeftAngle;

                circle114.Invalidate();
                circle114.angle = RightAngle;

                circle47.Invalidate();
                circle47.angle = RightAngle;

                circle48.Invalidate();
                circle48.angle = RightAngle;

                circle88.Invalidate();
                circle88.angle = RightAngle;

                circle89.Invalidate();
                circle89.angle = LeftAngle;

                circle105.Invalidate();
                circle105.angle = LeftAngle;



                // Cir26.Invalidate();
                //  Cir26.angle = RightAngle;

                circle106.Invalidate();
                circle106.angle = RightAngle;

                circle115.Invalidate();
                circle115.angle = LeftAngle;

                circle55.Invalidate();
                circle55.angle = LeftAngle;

                circle49.Invalidate();
                circle49.angle = LeftAngle;

                circle117.Invalidate();
                circle117.angle = RightAngle;

                circle113.Invalidate();
                circle113.angle = RightAngle;

                circle116.Invalidate();
                circle116.angle = LeftAngle;

                circle132.Invalidate();
                circle132.angle = RightAngle;

                circle29.Invalidate();
                circle29.angle = RightAngle;

                circle95.Invalidate();
                circle95.angle = RightAngle;
                //------------------------------
                circle27.Invalidate();
                circle27.angle = RightAngle;

                circle28.Invalidate();
                circle28.angle = LeftAngle;

                circle123.Invalidate();
                circle123.angle = LeftAngle;

                circle30.Invalidate();
                circle30.angle = LeftAngle;

                circle31.Invalidate();
                circle31.angle = RightAngle;

                circle32.Invalidate();
                circle32.angle = RightAngle;

                circle35.Invalidate();
                circle35.angle = LeftAngle;

                circle36.Invalidate();
                circle36.angle = RightAngle;

                circle37.Invalidate();
                circle37.angle = RightAngle;

                circle38.Invalidate();
                circle38.angle = RightAngle;

                circle39.Invalidate();
                circle39.angle = RightAngle;

                circle40.Invalidate();
                circle40.angle = LeftAngle;

                circle41.Invalidate();
                circle41.angle = RightAngle;

                circle130.Invalidate();
                circle130.angle = RightAngle;

                circle133.Invalidate();
                circle133.angle = RightAngle;
                //--------------

                circle149.Invalidate();
                circle149.angle = LeftAngle;

                circle142.Invalidate();
                circle142.angle = LeftAngle;
                circle143.Invalidate();
                circle143.angle = LeftAngle;
                circle144.Invalidate();
                circle144.angle = LeftAngle;
                circle145.Invalidate();
                circle145.angle = LeftAngle;
                circle146.Invalidate();
                circle146.angle = LeftAngle;

                RecoillerA.Invalidate();
                RecoillerA.angle = LeftAngle;
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        public void POR2Rotation()
        {
            try
            {


                RightAngle += 5;
                LeftAngle -= 5;
                if (RightAngle >= 360)
                    RightAngle = 0;

                if (LeftAngle <= -360)
                    LeftAngle = 0;


             //   circle100.Invalidate();
              //  circle100.angle = RightAngle;

              //  circle91.Invalidate();
               // circle91.angle = RightAngle;

                circle92.Invalidate();
                circle92.angle = LeftAngle;

                circle93.Invalidate();
                circle93.angle = RightAngle;

                circle94.Invalidate();
                circle94.angle = LeftAngle;

                circle96.Invalidate();
                circle96.angle = RightAngle;

                //Cir5.Invalidate();
                // Cir5.angle = RightAngle;


                circle97.Invalidate();
                circle97.angle = RightAngle;

                circle98.Invalidate();
                circle98.angle = LeftAngle;

                circle99.Invalidate();
                circle99.angle = LeftAngle;

                circle101.Invalidate();
                circle101.angle = LeftAngle;

                circle103.Invalidate();
                circle103.angle = LeftAngle;

                //  circle8.Invalidate();
                //  circle8.angle = LeftAngle;

                circle104.Invalidate();
                circle104.angle = LeftAngle;

                circle107.Invalidate();
                circle107.angle = LeftAngle;

                circle108.Invalidate();
                circle108.angle = LeftAngle;

                circle118.Invalidate();
                circle118.angle = LeftAngle;

                circle119.Invalidate();
                circle119.angle = LeftAngle;

                circle120.Invalidate();
                circle120.angle = LeftAngle;

                circle121.Invalidate();
                circle121.angle = LeftAngle;

                circle122.Invalidate();
                circle122.angle = LeftAngle;

                circle125.Invalidate();
                circle125.angle = LeftAngle;


                circle126.Invalidate();
                circle126.angle = LeftAngle;

                circle127.Invalidate();
                circle127.angle = RightAngle;

                circle128.Invalidate();
                circle128.angle = LeftAngle;

                circle129.Invalidate();
                circle129.angle = RightAngle;

                circle130.Invalidate();
                circle130.angle = RightAngle;

                circle131.Invalidate();
                circle131.angle = LeftAngle;

                circle45.Invalidate();
                circle45.angle = LeftAngle;

                //  circle21.Invalidate();
                //  circle21.angle = LeftAngle;

                circle44.Invalidate();
                circle44.angle = LeftAngle;


                /// ----------------------------------------------
                circle88.Invalidate();
                circle88.angle = LeftAngle;

                circle89.Invalidate();
                circle89.angle = LeftAngle;

                circle105.Invalidate();
                circle105.angle = RightAngle;

                circle106.Invalidate();
                circle106.angle = RightAngle;

                // circle25.Invalidate();
                // circle25.angle = LeftAngle;

                circle115.Invalidate();
                circle115.angle = LeftAngle;

                circle114.Invalidate();
                circle114.angle = RightAngle;

                circle47.Invalidate();
                circle47.angle = RightAngle;

                circle48.Invalidate();
                circle48.angle = RightAngle;

                circle88.Invalidate();
                circle88.angle = RightAngle;

                circle89.Invalidate();
                circle89.angle = LeftAngle;

                circle105.Invalidate();
                circle105.angle = LeftAngle;



                // Cir26.Invalidate();
                //  Cir26.angle = RightAngle;

                circle106.Invalidate();
                circle106.angle = RightAngle;

                circle115.Invalidate();
                circle115.angle = LeftAngle;

                circle55.Invalidate();
                circle55.angle = LeftAngle;

                circle49.Invalidate();
                circle49.angle = LeftAngle;

                circle117.Invalidate();
                circle117.angle = RightAngle;

                circle113.Invalidate();
                circle113.angle = RightAngle;

                circle116.Invalidate();
                circle116.angle = LeftAngle;

                circle132.Invalidate();
                circle132.angle = RightAngle;

                circle29.Invalidate();
                circle29.angle = RightAngle;

                circle95.Invalidate();
                circle95.angle = RightAngle;
                //------------------------------
                circle27.Invalidate();
                circle27.angle = RightAngle;

                circle28.Invalidate();
                circle28.angle = LeftAngle;

                circle123.Invalidate();
                circle123.angle = LeftAngle;

                circle30.Invalidate();
                circle30.angle = LeftAngle;

                circle31.Invalidate();
                circle31.angle = RightAngle;

                circle32.Invalidate();
                circle32.angle = RightAngle;

                circle35.Invalidate();
                circle35.angle = LeftAngle;

                circle36.Invalidate();
                circle36.angle = RightAngle;

                circle37.Invalidate();
                circle37.angle = RightAngle;

                circle38.Invalidate();
                circle38.angle = RightAngle;

                circle39.Invalidate();
                circle39.angle = RightAngle;

                circle40.Invalidate();
                circle40.angle = LeftAngle;

                circle41.Invalidate();
                circle41.angle = RightAngle;

                circle130.Invalidate();
                circle130.angle = RightAngle;

                circle133.Invalidate();
                circle133.angle = RightAngle;
                //------------------------------------
               // circle150.Invalidate();
               // circle150.angle = LeftAngle;

                circle137.Invalidate();
                circle137.angle = LeftAngle;
                circle141.Invalidate();
                circle141.angle = LeftAngle;


                circle151.Invalidate();
                circle151.angle = LeftAngle;

                RecoillerA.Invalidate();
                RecoillerA.angle = LeftAngle;
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }

        public void Recoiler1Rotation()
        {
            try
            {


                RightAngle += 5;
                LeftAngle -= 5;
                if (RightAngle >= 360)
                    RightAngle = 0;

                if (LeftAngle <= -360)
                    LeftAngle = 0;


                circle150.Invalidate();
                circle150.angle = LeftAngle;

                RecoillerA.Invalidate();
                RecoillerA.angle = LeftAngle;
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        public void Recoiler2Rotation()
        {
            try
            {


                RightAngle += 5;
                LeftAngle -= 5;
                if (RightAngle >= 360)
                    RightAngle = 0;

                if (LeftAngle <= -360)
                    LeftAngle = 0;
                circle91.Invalidate();
                circle91.angle = LeftAngle;
                circle100.Invalidate();
                circle100.angle = LeftAngle;

                circle46.Invalidate();
                circle46.angle = LeftAngle;


                // RecoillerB.Invalidate();
                //RecoillerB.angle = LeftAngle;
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        //Get Thickness and width value
        public void GetTrackingPOR1EventValue()
        {
            try
            {
                Db.DatabaseConnect();
                string s = "SELECT TC_WIDTH, TC_GUIDE_ORD_THICK FROM T_COL_COT_PDI_L3 WHERE TC_COIL_NUMBER ='" + txtPor1.Text + "'";              
                OracleCommand Cmd = new OracleCommand(s, Db.Con);
                OracleDataReader dr = Cmd.ExecuteReader();
                if (dr.Read())
                {
                    UserInfo.POR1EventThickness = dr["TC_GUIDE_ORD_THICK"].ToString();
                    UserInfo.POR1EventWidth = dr["TC_WIDTH"].ToString();
                }

                Db.ConClose();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        //After Click Start Button Coil Number,Width,Thickness Are Update
        public void UpdTrackEventPOR1()
        {
            try
            {
                GetTrackingPOR1EventValue();
                Db.DatabaseConnect();
                string s = "Update T_EVENT_TRACKING set COIL_ID='" + txtPor1.Text + "' ,WIDTH='"+ UserInfo.POR1EventWidth + "',THICKNESS='" + UserInfo.POR1EventThickness + "' where ID_APP_TAG_EVENT='L1_POR_SEL'";
                OracleCommand Cmd = new OracleCommand(s, Db.Con);
                Cmd.ExecuteNonQuery();
                Db.ConClose();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        //Por1 Btn Start
        private void btnPor1_Click(object sender, EventArgs e)
        {
            try
            {
            //    if (diagonal2.BackLineColor == Color.Red)
            //    {
            //        MessageBox.Show("POR2 Section is processing");
            //    }
              //  else
                if (btnPor1.BackColor == Color.FromArgb(59, 89, 152))
                {

                    if (txtPor1.Text != String.Empty)
                    {
                        OPC.SendTCPData("L1_POR1_SEL", 1);
                        btnPor1.BackColor = Color.Red;
                        btnPor1.Text = "Processing";
                       
                        btnPor2.BackColor = Color.FromArgb(59, 89, 152);
                        btnPor2.Enabled = false;
                        //tRACKING EVENT
                        //UpdTrackEventPOR1();
                      
                        Por1timer.Start();
                       
                        timerEntry.Start();
                        //BtnTrackig For Level2 Page
                        UserInfo.PORBtnTracking = "POR1 SEL";
                       
                    }
                    else
                    {
                        MessageBox.Show("Coil are not in POR1 Section", "CCL", MessageBoxButtons.OK, MessageBoxIcon.Information);
                        btnPor1.BackColor = Color.FromArgb(59, 89, 152);
                        btnPor2.BackColor = Color.FromArgb(59, 89, 152);
                        btnPor2.Enabled = true;
                    }
                }
                else if (btnPor1.BackColor == Color.Red)
                {

                    //Por1timer.Stop();

                    //timerEntry.Stop();
                    //timerColor.Stop();
                    //btnPor1.BackColor = Color.FromArgb(59, 89, 152);
                    //btnPor2.Enabled = true;
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        public void GetTrackingPOR2EventValue()
        {
            try
            {
                Db.DatabaseConnect();
                string s = "SELECT TC_WIDTH, TC_GUIDE_ORD_THICK FROM T_COL_COT_PDI_L3 WHERE TC_COIL_NUMBER ='" + txtPor2.Text + "'";
                OracleCommand Cmd = new OracleCommand(s, Db.Con);
                OracleDataReader dr = Cmd.ExecuteReader();
                if (dr.Read())
                {
                    UserInfo.POR2EventThickness = dr["TC_GUIDE_ORD_THICK"].ToString();
                    UserInfo.POR2EventWidth = dr["TC_WIDTH"].ToString();
                }

                Db.ConClose();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        public void UpdTrackEventPOR2()
        {
            try
            {
                GetTrackingPOR2EventValue();
                Db.DatabaseConnect();
                string s = "Update T_EVENT_TRACKING set COIL_ID='" + txtPor2.Text + "',WIDTH='"+ UserInfo.POR2EventWidth + "',THICKNESS='" + UserInfo.POR2EventThickness + "' where ID_APP_TAG_EVENT='L1_POR_SEL'";
                OracleCommand Cmd = new OracleCommand(s, Db.Con);
                Cmd.ExecuteNonQuery();
                Db.ConClose();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message); 
            }
        }
        private void btnPor2_Click(object sender, EventArgs e)
        {
            try
            {
                if (diagonal1.BackLineColor == Color.Red)
                {
                    MessageBox.Show("POR1 Section is processing");
                }
                else if (btnPor2.BackColor == Color.FromArgb(59, 89, 152))
                {
                    
                    if (txtPor2.Text != String.Empty)
                    {
                        OPC.SendTCPData("L1_POR2_SEL", 1);
                        btnPor2.BackColor = Color.Red;
                        btnPor2.Text = "Processing";
                       
                        btnPor1.BackColor = Color.FromArgb(59, 89, 152);
                        btnPor1.Enabled = false;
                        Por2timer.Start();
                        //Tracking Event
                       // UpdTrackEventPOR2();
                        
                        timerEntry.Start();
                        dat = DateTime.Now;
                        //BtnTrackig For Level2 Pagee
                        UserInfo.PORBtnTracking = "POR2 SEL";
                       
                    }
                    else
                    {
                        MessageBox.Show("Coil are not in POR2 Section", "CCL", MessageBoxButtons.OK, MessageBoxIcon.Information);
                        btnPor1.BackColor = Color.FromArgb(59, 89, 152);
                        btnPor2.BackColor = Color.FromArgb(59, 89, 152);
                        btnPor1.Enabled = true;
                    }
                }
                else if (btnPor2.BackColor == Color.Red)
                {
                    //Por2timer.Stop();
                   
                    //timerEntry.Stop();
                    //timerColor.Stop();
                    //btnPor2.BackColor = Color.FromArgb(59, 89, 152);
                    //btnPor1.Enabled = true;
                }
                else
                {

                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);

            }
        }
        public void MillActualValue()
        {
            Db.DatabaseConnect();
            string query = "select  PROCESS_LINE_SPEED_ACT FROM T_PERIODIC_VALUE_LOG  ORDER BY DATE_TIME DESC  fetch first 1 row only";
            OracleDataAdapter oda = new OracleDataAdapter(query, Db.Con);
            DataTable dtActual = new DataTable();
            oda.Fill(dtActual);
            if (dtActual.Rows.Count > 0)
            {
                txtSpeed.Text = dtActual.Rows[0].ItemArray[0].ToString();

                if(txtSpeed.Text=="0")
                {
                //    Por1timer.Stop();
                //    Por2timer.Stop();
                //    Recoiler1_Timer.Stop();
                //    Recoiler2_Timer.Stop();
                }
                else
                {

                    Db.DatabaseConnect();
                    string queryT_UNCOILER_SEL = "select * from T_UNCOILER_SEL FETCH order by WRITE_TIMESTAMP DESC fetch first 1 row only";
                    OracleDataAdapter odaT_UNCOILER_SEL = new OracleDataAdapter(queryT_UNCOILER_SEL, Db.Con);
                    DataTable dtT_UNCOILER_SEL = new DataTable();
                    odaT_UNCOILER_SEL.Fill(dtT_UNCOILER_SEL);
                    if(dtT_UNCOILER_SEL.Rows.Count > 0)
                    {
                        if (dtT_UNCOILER_SEL.Rows[0][0].ToString()== "UNC_1_SELECTED")
                        {

                            Por1timer.Start();
                            Por2timer.Stop();

                        }
                        else
                        {

                            Por2timer.Start();
                            Por1timer.Stop();
                        }
                    }

                    string queryT_RECOILER_SEL = "select * from T_RECOILER_SEL FETCH order by WRITE_TIMESTAMP DESC fetch first 1 row only";
                    OracleDataAdapter odaT_RECOILER_SEL = new OracleDataAdapter(queryT_RECOILER_SEL, Db.Con);
                    DataTable dtT_RECOILER_SEL = new DataTable();
                    odaT_RECOILER_SEL.Fill(dtT_RECOILER_SEL);
                    if (dtT_RECOILER_SEL.Rows.Count > 0)
                    {
                        if (dtT_RECOILER_SEL.Rows[0][0].ToString() == "RNC_1_SELECTED")
                        {

                            Recoiler1_Timer.Start();
                            Recoiler2_Timer.Stop();

                        }
                        else
                        {
                            Recoiler2_Timer.Start();
                            Recoiler1_Timer.Stop();

                        }
                    }
                    Db.ConClose();




                }
            
               
                Por1timer.Interval = 1;
                Por1timer.Start();
              
            }
        }
        //Accumulator
        public void EtryExitAccumulator()
        {
            try
            {
                Db.DatabaseConnect();
                string query = "select  ENTRY_ACCUMULATOR_POS_PERCENT FROM T_PERIODIC_VALUE_LOG  ORDER BY DATE_TIME DESC  fetch first 1 row only";
                OracleDataAdapter oda = new OracleDataAdapter(query, Db.Con);
                DataTable dtActual = new DataTable();
                oda.Fill(dtActual);
                if (dtActual.Rows.Count > 0)
                {
                    if (Convert.ToInt32(dtActual.Rows[0].ItemArray[0]) >= 100)
                    {
                        // txtActual.Text = dtActual.Rows[0].ItemArray[0].ToString();
                        progressBarEntry.Value = 100;
                    }
                    else {
                        // txtActual.Text = dtActual.Rows[0].ItemArray[0].ToString();
                        progressBarEntry.Value = Convert.ToInt32(dtActual.Rows[0].ItemArray[0]);
                    }
                   
                }


                ////progressBarEntry.Increment(7);
                //Random rnd = new Random();

                //for (int i = 0; i < 5; i++)
                //{
                //    progressBarEntry.Value = rnd.Next(1, 99);

                //}



                // lblEntryAccu.Text = progressBarEntry.Value.ToString() + "%";
                label72.Text = progressBarEntry.Value.ToString() + "%";
                // pictEntAccu.Height = 200 - progressBarEntry.Value * 2;
                pictureBox45.Height = 200 - progressBarEntry.Value * 2;
                
                //txtSet.Text = progressBarEntry.Value.ToString();

                //progressBar Exit;

                string query1 = "select  EXIT_ACCUMULATOR_POS_PERCENT FROM T_PERIODIC_VALUE_LOG  ORDER BY DATE_TIME DESC  fetch first 1 row only";
                OracleDataAdapter oda1 = new OracleDataAdapter(query1, Db.Con);
                DataTable dtActual1 = new DataTable();
                oda1.Fill(dtActual1);
                if (dtActual1.Rows.Count > 0)
                {
                    if (Convert.ToInt32(dtActual1.Rows[0].ItemArray[0]) >= 100)
                    {
                        // txtActual.Text = dtActual.Rows[0].ItemArray[0].ToString();
                        progressBarExit.Value = 100;
                    }
                    else
                    {
                        // txtActual.Text = dtActual.Rows[0].ItemArray[0].ToString();
                        progressBarExit.Value = Convert.ToInt32(dtActual1.Rows[0].ItemArray[0]);

                    }
                    
                }




                //Random rnd1 = new Random();

                //for (int i = 0; i < 8; i++)
                //{
                //    progressBarExit.Value = rnd1.Next(1, 99);
                //}
                // lblExitAccu.Text = progressBarExit.Value.ToString() + "%";
                label73.Text = progressBarExit.Value.ToString() + "%";
                
                //txtActual.Text = (progressBarExit.Value + 1).ToString();
                pictureBox47.Height = 200 - progressBarExit.Value * 2;
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        private void timerEntry_Tick(object sender, EventArgs e)
        {
            //try
            //{
               
            //    //progressBarEntry.Increment(7);
            //    Random rnd = new Random();

            //    for (int i = 0; i < 5; i++)
            //    {
            //        progressBarEntry.Value = rnd.Next(1, 99);
            //    }
            //    lblEntryAccu.Text = progressBarEntry.Value.ToString() + "%";
            //    pictEntAccu.Height = 200 - progressBarEntry.Value * 2;
            //   // txtSet.Text = progressBarEntry.Value.ToString();

            //    //progressBar Exit;

            //    Random rnd1 = new Random();
               
            //    for (int i = 0; i < 8; i++)
            //    {
            //        progressBarExit.Value = rnd1.Next(1, 99);
            //    }
            //    lblExitAccu.Text = progressBarExit.Value.ToString() + "%";
            //    //txtActual.Text = (progressBarExit.Value + 1).ToString();
            //    pictExitAccumulator.Height = 200 - progressBarExit.Value * 2;
            //}
            //catch (Exception ex)
            //{
            //    MessageBox.Show(ex.Message);
            //}
        }

        //Rotate();
        private void Por1timer_Tick(object sender, EventArgs e)
        {
            //txtTimerStatus.Text = (Convert.ToInt32(txtTimerStatus.Text)+1).ToString();
            //POR1Rotation();                     
        }
        private void Por2timer_Tick(object sender, EventArgs e)
        {
            POR2Rotation();
        }
        public void PorColorBlack()
        {
            try
            {
                pict1.BackColor = Color.Black;
              //  pict2.BackColor = Color.Black;
                pict3.BackColor = Color.Black;
             //   pict4.BackColor = Color.Black;
                pict5.BackColor = Color.Black;
                pict6.BackColor = Color.Black;
                pict7.BackColor = Color.Black;
                pict8.BackColor = Color.Black;
                pict9.BackColor = Color.Black;
                pict10.BackColor = Color.Black;
                pict11.BackColor = Color.Black;
                pict12.BackColor = Color.Black;
                pict13.BackColor = Color.Black;
                pict14.BackColor = Color.Black;
                pict15.BackColor = Color.Black;
                pict16.BackColor = Color.Black;
                pict17.BackColor = Color.Black;
                pict18.BackColor = Color.Black;
                pict19.BackColor = Color.Black;
                pict20.BackColor = Color.Black;
                pict21.BackColor = Color.Black;
                pict22.BackColor = Color.Black;
                pict23.BackColor = Color.Black;
                pict24.BackColor = Color.Black;
                pict25.BackColor = Color.Black;
                pict26.BackColor = Color.Black;
                pict27.BackColor = Color.Black;
                pict28.BackColor = Color.Black;
                pict29.BackColor = Color.Black;
                pict30.BackColor = Color.Black;
                pict31.BackColor = Color.Black;
                pict32.BackColor = Color.Black;
                pict33.BackColor = Color.Black;
                pict34.BackColor = Color.Black;
                pict35.BackColor = Color.Black;
                pict36.BackColor = Color.Black;
                pict37.BackColor = Color.Black;
                pict38.BackColor = Color.Black;
                pict39.BackColor = Color.Black;
                pict40.BackColor = Color.Black;
                pict41.BackColor = Color.Black;
                pict42.BackColor = Color.Black;
                pict43.BackColor = Color.Black;
                pict44.BackColor = Color.Black;
                pict45.BackColor = Color.Black;
                diagonal1.Invalidate();
                diagonal1.BackLineColor = Color.Black;
               // diagonal2.Invalidate();
               // diagonal2.BackLineColor = Color.Black;
                diagonal3.Invalidate();
                diagonal3.BackLineColor = Color.Black;
                diagonal4.Invalidate();
                diagonal4.BackLineColor = Color.Black;
                diagonal5.Invalidate();
                diagonal5.BackLineColor = Color.Black;
                diagonal6.Invalidate();
                diagonal6.BackLineColor = Color.Black;
                diagonal7.Invalidate();
                diagonal7.BackLineColor = Color.Black;

                diagonalSep1.Invalidate();
                diagonalSep1.backLineColorSep = Color.Black;
                diagonalSep2.Invalidate();
                diagonalSep2.backLineColorSep = Color.Black;
                diagonalSep3.Invalidate();
                diagonalSep3.backLineColorSep = Color.Black;
                diagonalSep4.Invalidate();
                diagonalSep4.backLineColorSep = Color.Black;
                diagonalSep5.Invalidate();
                diagonalSep5.backLineColorSep = Color.Black;
                diagonalSep6.Invalidate();
                diagonalSep6.backLineColorSep = Color.Black;
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }

        public void SaveRecoillerCoil()
        {
            try
            {
                //Db.DatabaseConnect();
                //string s = "insert into T_TRACK_RECOILLER values ('" + txtRecoiller.Text + "' ,'" + DateTime.Now.ToString(" dd MMM yyyy hh:mm:ss tt") + "')";
                //string s = "insert into T_TRACK_RECOILLER (COIL_ID,TOC) values (:COIL_ID,systimestamp)";
                //OracleCommand cmd = new OracleCommand(s, Db.Con);
                //cmd.CommandType = CommandType.Text;
                //cmd.Parameters.Add(":COIL_ID", OracleDbType.Varchar2).Value = Convert.ToString(txtRecoiller.Text);
                //cmd.ExecuteNonQuery();
                //Db.ConClose();

                Db.DatabaseConnect();

                //string s = "insert into T_TRACK_RECOILLER values ('" + txtRecoiller.Text + "' ,'" + DateTime.Now.ToString(" dd MMM yyyy hh:mm:ss tt") + "')";
                string s = "insert into T_TRACK_RECOILE (COIL_ID,ENTRY_LOC,TIME,RECOILLER_TIME) values (:COIL_ID,:ENTRY_LOC,:TIME,systimestamp)";
                OracleCommand cmd = new OracleCommand(s, Db.Con);
                cmd.CommandType = CommandType.Text;
                if (btnPor1.BackColor == Color.Red)
                {
                    
                    string LOCATION = "POR1";
                    cmd.Parameters.Add(":COIL_ID", OracleDbType.Varchar2).Value = Convert.ToString(txtRecoiller.Text);
                    cmd.Parameters.Add(":ENTRY_LOC", OracleDbType.Varchar2).Value = LOCATION;
                    cmd.Parameters.Add(":TIME", OracleDbType.TimeStamp).Value = dat;
                    cmd.ExecuteNonQuery();
                   
                }
               else if (btnPor2.BackColor == Color.Red)
                {
                    string LOCATION2 = "POR2";
                    cmd.Parameters.Add(":COIL_ID", OracleDbType.Varchar2).Value = Convert.ToString(txtRecoiller.Text);
                    cmd.Parameters.Add(":ENTRY_LOC", OracleDbType.Varchar2).Value = LOCATION2;
                    cmd.Parameters.Add(":TIME", OracleDbType.TimeStamp).Value = dat;
                    cmd.ExecuteNonQuery();

                }
            Db.ConClose();
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error:"+ ex.Message);
            }
        }
        public void UpdateCoilLoc()
        {
            try
            {
                Db.DatabaseConnect();
                string s = "Update T_COIL_LOC set COIL_ID='" + null + "' , SEQ_NO='" + null + "',TOC='" + null + "',TOM='" + null + "' where COIL_ID='" + txtRecoiller.Text + "'";
                OracleCommand Cmd = new OracleCommand(s, Db.Con);
                Cmd.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error:-"+" "+ ex.Message);
            }
        }
        //Full Processing
        public void UpdatePdiFlag()
        {
            try
            {
                string Flag = "S";
                string UpdPdi = "update T_COL_COT_PDI_L3 SET FLAG='" + Flag + "'  where TC_COIL_NUMBER='" + txtRecoiller.Text + "'";
                OracleCommand Cmd1 = new OracleCommand(UpdPdi, Db.Con);
                Cmd1.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error:-"+" "+ ex.Message);
            }
        }
        //Full Processing
        public void UpdatesETPointFlag()
        {
            try
            {
                string Flag = "S";
                string UpdPdi = "update T_COL_L2_SETPT_CALC SET FLAG='" + Flag + "'  where TCIP_INPUT_COIL='" + txtRecoiller.Text + "'";
                OracleCommand Cmd1 = new OracleCommand(UpdPdi, Db.Con);
                Cmd1.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error:-" + " " + ex.Message);
            }
        }
        //Coil PROCESS Complete
        public void DeleteRecoillerCoil()
        {
            //UpdateCoilLoc();// UPDATED on 17.06.24
            //UpdatePdiFlag();// UPDATED on 17.06.24
            //UpdatesETPointFlag();// UPDATED on 17.06.24

        }
        public void Por1CoilMove()
        {
            try
            {                
                //Requirement Change
                //if (txtPor1c3.Text != string.Empty && txtPor1c2.Text == string.Empty && txtPor1c1.Text == string.Empty)
                //{
                //    txtPor1.Text = txtPor1c3.Text;
                //    txtPor1c3.Text = "";
                //}
                //else if (txtPor1c3.Text != string.Empty && txtPor1c2.Text != string.Empty && txtPor1c1.Text == string.Empty)
                //{
                //    txtPor1.Text = txtPor1c3.Text;
                //    txtPor1c3.Text = txtPor1c2.Text;
                //    txtPor1c2.Text = "";
                //}
                //else if (txtPor1c3.Text != string.Empty && txtPor1c2.Text != string.Empty && txtPor1c1.Text != string.Empty)
                //{
                //    txtPor1.Text = txtPor1c3.Text;
                //    txtPor1c3.Text = txtPor1c2.Text;
                //    txtPor1c2.Text = txtPor1c1.Text;
                //    txtPor1c1.Text = "";
                //}
                //else
                //{

                //}
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        public void Por2CoilMove()
        {
            try
            {
                if (txtPor2c3.Text != string.Empty && txtPor2c2.Text == string.Empty && txtPor2c1.Text == string.Empty)
                {
                    txtPor2.Text = txtPor2c3.Text;
                    txtPor2c3.Text = "";
                }
                else if (txtPor2c3.Text != string.Empty && txtPor2c2.Text != string.Empty && txtPor2c1.Text == string.Empty)
                {
                    txtPor2.Text = txtPor2c3.Text;
                    txtPor2c3.Text = txtPor2c2.Text;
                    txtPor2c2.Text = "";
                }
                else if (txtPor2c3.Text != string.Empty && txtPor2c2.Text != string.Empty && txtPor2c1.Text != string.Empty)
                {
                    txtPor2.Text = txtPor2c3.Text;
                    txtPor2c3.Text = txtPor2c2.Text;
                    txtPor2c2.Text = txtPor2c1.Text;
                    txtPor2c1.Text = "";
                }
                else
                {

                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        public void RecoilerShowPIC()
        {
            try
            {

                if (txtRecoiler1.Text != string.Empty)
                {
                    PictRecoiler1.Visible = true;
                    pictRecoiler1S.Visible = false;
                }
                if (txtRecoiler2.Text != string.Empty)
                {
                    PictRecoiler2.Visible = true;
                    pictRecoiler2S.Visible = false;
                }
                if (txtRecoiler3.Text != string.Empty)
                {
                    PictRecoiler3.Visible = true;
                    pictRecoiler3S.Visible = false;
                }
                else
                {
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }

        }
        public void RecoillerMovePor1()
        {
            try

            {
                if (txtRecoiller.Text == string.Empty)
                {
                    txtRecoiller.Text = txtPor1.Text;
                }
                else if (txtRecoiler1.Text == string.Empty && txtRecoiller.Text != string.Empty)
                {
                    txtRecoiler1.Text = txtRecoiller.Text;
                    txtRecoiller.Text = txtPor1.Text;

                }
                else if (txtRecoiler2.Text == string.Empty && txtRecoiler1.Text != string.Empty && txtRecoiller.Text != string.Empty)
                {
                    //txt2Recoiler2.Text = txt1Recoiler1.Text;
                    //txt2Recoiler1.Text = txt1Recoiller.Text;
                    //txt2Recoiller.Text = txt1Por1.Text;

                    txtRecoiler2.Text = txtRecoiler1.Text;
                    txtRecoiler1.Text = txtRecoiller.Text;
                    txtRecoiller.Text= txtPor1.Text;

                }
                else if (txtRecoiler3.Text == string.Empty && txtRecoiler2.Text != string.Empty && txtRecoiler1.Text != string.Empty && txtRecoiller.Text != string.Empty)
                {
                    //txt1Recoiler3.Text = txt1Recoiler2.Text;
                    //txt1Recoiler2.Text = txt1Recoiler1.Text;
                    //txt1Recoiler1.Text = txt1Recoiller.Text;
                    //txt1Recoiller.Text = txt1Por1.Text;

                    txtRecoiler3.Text = txtRecoiler2.Text;
                    txtRecoiler2.Text = txtRecoiler1.Text;
                    txtRecoiler1.Text = txtRecoiller.Text;
                    txtRecoiller.Text = txtPor1.Text;

                }
                else
                {

                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);

            }

        }
        //Recoiller Move for Por2 section
        public void RecoillerMovePor2()
        {
            try
            {
                if (txtRecoiller.Text == string.Empty)
                {
                    txtRecoiller.Text = txtPor2.Text;
                }
                else if (txtRecoiler1.Text == string.Empty && txtRecoiller.Text != string.Empty)
                {
                    txtRecoiler1.Text = txtRecoiller.Text;
                    txtRecoiller.Text = txtPor2.Text;

                }
                else if (txtRecoiler2.Text == string.Empty && txtRecoiler1.Text != string.Empty && txtRecoiller.Text != string.Empty)
                {
                    txtRecoiler2.Text = txtRecoiler1.Text;
                    txtRecoiler1.Text = txtRecoiller.Text;
                    txtRecoiller.Text = txtPor2.Text;

                }
                else if (txtRecoiler3.Text == string.Empty && txtRecoiler2.Text != string.Empty && txtRecoiler1.Text != string.Empty && txtRecoiller.Text != string.Empty)
                {
                    //txt1Recoiler3.Text = txt2Recoiler2.Text;
                    //txt1Recoiler2.Text = txt1Recoiler1.Text;
                    //txt2Recoiler1.Text = txt2Recoiller.Text;
                    //txt1Recoiller.Text = txt1Por2.Text;

                    txtRecoiler3.Text= txtRecoiler2.Text;
                    txtRecoiler2.Text=txtRecoiler1.Text;
                    txtRecoiler1.Text = txtRecoiller.Text;
                    txtRecoiller.Text=txtPor2.Text;

                }
                else
                {

                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }      
        //public void Por1ColorProcessing()
        //{
        //    try
        //    {
        //        //int value = count++;
        //        //lblresult.Text = value.ToString();
        //        if (lblresult.Text == "1")
        //        {
        //            diagonal1.Invalidate();
        //            diagonal1.BackLineColor = Color.Red;
        //        }
        //        if (lblresult.Text == "2")
        //        {
        //            pict1.BackColor = Color.Red;
        //        }                
        //        //REQUIREMENT MANAGE
        //            if (lblresult.Text == "2012")
        //        {

        //            RecoillerMovePor1();
        //            RecoilerShowPIC();
        //            SaveRecoillerCoil();
        //            //Process Complete
        //            //DeleteRecoillerCoil(); //updated on 17.06.24
        //            //txtPor1.Text = "";  //updated on 17.06.24
        //            btnPor1.Text = "START";
        //            //Requirment Change
        //            //Por1CoilMove();
        //           // POR1CoilShowPic();
        //            count = 1;
        //            Por1timer.Stop();
        //            //reset coil
        //            //por1Black();
        //            btnPor1.BackColor = Color.FromArgb(59, 89, 152);
        //            btnPor1.Enabled = true;
        //            btnPor2.Enabled = true;
        //            timerEntry.Stop();

        //            progressBarEntry.Value = 50;
        //            progressBarExit.Value = 50;

        //            //lblEntryAccu.Text = progressBarEntry.Value.ToString() + "%";
        //            lblEntryAccu.Text = 0 + "%";
        //            //pictEntAccu.Height = 200 - progressBarEntry.Value * 2;
        //            pictEntAccu.Height = 0;

        //            lblExitAccu.Text = 0 + "%";
        //            //pictExitAccumulator.Height = 200 - progressBarExit.Value * 2;
        //            pictExitAccumulator.Height = 0;
        //            timerColor.Stop();
        //        }
        //        else
        //        {

        //        }
        //    }
        //    catch (Exception ex)
        //    {
        //        MessageBox.Show(ex.Message);
        //    }
        //}
        //Requirement Change
        //public void POR1CoilShowPic()
        //{
        //    try
        //    {
        //        if (txtPor1c3.Text != string.Empty)
        //        {
        //            PictPor1c3.Visible = true;
        //            PictPor1c3S.Visible = false;
        //        }
        //        if (txtPor1c2.Text != string.Empty)
        //        {
        //            PictPor1c2.Visible = true;
        //            PictPor1c2S.Visible = false;
        //        }
        //        if (txtPor1c1.Text != string.Empty)
        //        {
        //            PictPor1c1.Visible = true;
        //            PictPor1c1S.Visible = false;
        //        }
        //        if (txtPor1c3.Text == string.Empty)
        //        {
        //            PictPor1c3.Visible = false;
        //            PictPor1c3S.Visible = true;
        //        }
        //        if (txtPor1c2.Text == string.Empty)
        //        {
        //            PictPor1c2.Visible = false;
        //            PictPor1c2S.Visible = true;
        //        }
        //        if (txtPor1c1.Text == string.Empty)
        //        {
        //            PictPor1c1.Visible = false;
        //            PictPor1c1S.Visible = true;
        //        }
        //        else
        //        {

        //        }
        //    }
        //    catch (Exception ex)
        //    {
        //        MessageBox.Show(ex.Message);
        //    }

        //}
        //Requirement Change
        //public void POR2CoilShowPic()
        //{
        //    try
        //    {
        //        if (txtPor2c3.Text != string.Empty)
        //        {
        //            PictPor2c3.Visible = true;
        //            PictPor2c3S.Visible = false;
        //        }
        //        if (txtPor2c2.Text != string.Empty)
        //        {
        //            PictPor2c2.Visible = true;
        //            PictPor2c2S.Visible = false;
        //        }
        //        if (txtPor2c1.Text != string.Empty)
        //        {
        //            PictPor2c1.Visible = true;
        //            PictPor2c1S.Visible = false;
        //        }
        //        if (txtPor2c3.Text == string.Empty)
        //        {
        //            PictPor2c3.Visible = false;
        //            PictPor2c3S.Visible = true;
        //        }
        //        if (txtPor2c2.Text == string.Empty)
        //        {
        //            PictPor2c2.Visible = false;
        //            PictPor2c2S.Visible = true;
        //        }
        //        if (txtPor2c1.Text == string.Empty)
        //        {
        //            PictPor2c1.Visible = false;
        //            PictPor2c1S.Visible = true;
        //        }
        //        else
        //        {

        //        }
        //    }
        //    catch (Exception ex)
        //    {
        //        MessageBox.Show(ex.Message);
        //    }
        //}
        public void Por2ColorProcessing()
        {
            try
            {
                if (lblresult.Text == "1")
                {
                  //  diagonal2.Invalidate();
                  //  diagonal2.BackLineColor = Color.Red;
                }
                if (lblresult.Text == "2")
                {
                   // pict2.BackColor = Color.Red;
                }               
                //Recoiller
               
                if (lblresult.Text == "16710")
                    {
                    //    RecoillerMovePor2();
                    //    RecoilerShowPIC();
                    //    SaveRecoillerCoil();
                    //    DeleteRecoillerCoil();
                    //    txtPor2.Text = "";
                    //    //Requirement Change
                    //    //Por2CoilMove();
                    //    //POR2CoilShowPic();
                    //    Por2timer.Stop();
                    //    count = 1;
                    //    //por1Black();
                    //    pict2.BackColor = Color.Black;
                    //    btnPor2.BackColor = Color.FromArgb(59, 89, 152);
                    //    btnPor1.Enabled = true;
                    //    btnPor2.Enabled = true;
                    //    btnPor2.Text = "START";
                    //    timerEntry.Stop();
                    //----------New Code ----------------------//
                 string PorCoilNumb = Convert.ToString(txtPor1.Text);
                //Por1timer.Stop();
                // timerEntry.Stop();
               
                btnPor1.BackColor = Color.FromArgb(59, 89, 152);
                btnPor1.Text = "START";
                btnPor2.Enabled = true;
                //EndPor1TrackCoilDetails(PorCoilNumb);
                //EndPOR1UpdatePdiFlag();
                //EndPOR1UpdatesETPointFlag();
                EndPor1UpdateCoilLoc();
                txtPor1.Text = "";
                //Sending Coil Completion data from POR

                EndPor1Timer.Start();
                OPC.SendTCPData("L1_POR_CMPL", 1);
                    //----------New Code  End ----------------------//


                    progressBarEntry.Value = 0;
                    progressBarExit.Value = 0;

                    //lblEntryAccu.Text = progressBarEntry.Value.ToString() + "%";
                    lblEntryAccu.Text = 0 + "%";
                    //pictEntAccu.Height = 200 - progressBarEntry.Value * 2;
                    pictEntAccu.Height = 0;

                    //lblExitAccu.Text = progressBarExit.Value.ToString() + "%";
                    lblExitAccu.Text = 0 + "%";
                    //pictExitAccumulator.Height = 200 - progressBarExit.Value * 2;
                    pictExitAccumulator.Height = 0;
                    
                }
                else
                {

                }
            }
            catch (Exception ex) 
            {
                MessageBox.Show(ex.Message);
            }
        }
        private void timerColor_Tick(object sender, EventArgs e)
        { 
        }
        //PDI Flag Update
        //public void EndPOR1UpdatePdiFlag()
        //{
        //    try
        //    {
        //        Db.DatabaseConnect();
        //        string Flag = "S";
        //        string UpdPdi = "update T_COL_COT_PDI_L3 SET FLAG='" + Flag + "'  where TC_COIL_NUMBER='" + txtPor1.Text + "'";
        //        OracleCommand Cmd1 = new OracleCommand(UpdPdi, Db.Con);
        //        Cmd1.ExecuteNonQuery();
        //        Db.ConClose();
        //    }
        //    catch (Exception ex)
        //    {
        //        MessageBox.Show("Error:-" + " " + ex.Message);
        //    }
        //}
        //SetPoint Flag Update 
        //public void EndPOR1UpdatesETPointFlag()
        //{
        //    try
        //    {
        //        Db.DatabaseConnect();
        //        string Flag = "S";
        //        string UpdPdi = "update T_COL_L2_SETPT_CALC SET FLAG='" + Flag + "'  where TCIP_INPUT_COIL='" + txtPor1.Text + "'";
        //        OracleCommand Cmd1 = new OracleCommand(UpdPdi, Db.Con);
        //        Cmd1.ExecuteNonQuery();
        //        Db.ConClose();
        //    }
        //    catch (Exception ex)
        //    {
        //        MessageBox.Show("Error:-" + " " + ex.Message);
        //    }
        //}
        //Insert Coil Processing Details
        //public void EndPor1TrackCoilDetails( string CoilNumberEnd)
        //{
        //    Db.DatabaseConnect();
        //    string s = "insert into T_TRACK_RECOILE (COIL_ID,ENTRY_LOC,TIME,RECOILLER_TIME) values (:COIL_ID,:ENTRY_LOC,:TIME,systimestamp)";
        //    OracleCommand cmd = new OracleCommand(s, Db.Con);
        //    cmd.CommandType = CommandType.Text;

        //    string LOCATION = "POR1";
        //    // cmd.Parameters.Add(":COIL_ID", OracleDbType.Varchar2).Value = Convert.ToString(txtPor1.Text);
        //    cmd.Parameters.Add(":COIL_ID", OracleDbType.Varchar2).Value = CoilNumberEnd;
        //    cmd.Parameters.Add(":ENTRY_LOC", OracleDbType.Varchar2).Value = LOCATION;
        //    cmd.Parameters.Add(":TIME", OracleDbType.TimeStamp).Value = dat;
        //    cmd.ExecuteNonQuery();
        //    Db.ConClose();
        //}
        //Coil Location Update
        public void EndPor1UpdateCoilLoc()
        {
            try
            {
                Db.DatabaseConnect();
                string s = "Update T_COIL_LOC set COIL_ID='" + null + "' , SEQ_NO='" + null + "',TOC='" + null + "',TOM='" + null + "' where COIL_ID='" + txtPor1.Text + "'";
                OracleCommand Cmd = new OracleCommand(s, Db.Con);
                Cmd.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error:-" + " " + ex.Message);
            }
        }
        private void btnPor1End_Click(object sender, EventArgs e)
        {
            try
            {
                if (btnPor1.Text == "START")
                {
                    MessageBox.Show("Can not end, unprocessed coil");
                }
                else
                {
                    if (txtPor1.Text != string.Empty)
                    {
                        Por1timer.Stop();
                        // timerEntry.Stop();
                        count = 1;
                       
                        btnPor1.BackColor = Color.FromArgb(59, 89, 152);
                        btnPor1.Text = "START";
                        btnPor2.Enabled = true;
                        //Rotation Stop
                      
                        OPC.SendTCPData("L1_POR1_SEL", 0);
                        Por1timer.Stop();
                        PorColorBlack();
                        txtPor1.Text = "";
                       
                        //updated on 17.06.24 
                        //string PorCoilNumb = Convert.ToString(txtPor1.Text);
                        //EndPor1TrackCoilDetails(PorCoilNumb);
                        //EndPOR1UpdatePdiFlag();
                        //EndPOR1UpdatesETPointFlag();
                        //EndPor1UpdateCoilLoc();

                        //Sending Coil Completion data from POR

                        //EndPor1Timer.Start(); 

                    }
                    else
                    {

                    }
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
           
        }
        //ENDpOR1 Process

        private void EndPor1Timer_Tick(object sender, EventArgs e)
        {
            try
            {
                int EndValue = EndPor1Count++;
                lblEndPor1.Text = EndValue.ToString();

                if (lblEndPor1.Text == "1")
                {
                    diagonal1.Invalidate();
                    diagonal1.BackLineColor = Color.Black;
                }
                if (lblEndPor1.Text == "2")
                {
                    pict1.BackColor = Color.Black;
                }
                if (lblEndPor1.Text == "3")
                {
                    diagonalSep1.Invalidate();
                    diagonalSep1.backLineColorSep = Color.Black;

                    //commented by jitu on 22/05/2025
                    // EndPor1Timer.Stop();
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }


        //BTN POR2END
        private void btnEnd_Click(object sender, EventArgs e)
        {
            try
            {
                //Sending Coil Completion data from POR
                //string Por2CoilNumb = Convert.ToString(txtPor2.Text);

                if (btnPor2.Text == "START")
                {
                    MessageBox.Show("Can not end unprocessed coil");
                }
                else
                {
                    if (txtPor2.Text != string.Empty)
                    {
                       // count = 1;
                       
                        OPC.SendTCPData("L1_POR2_SEL", 0 );
                        btnPor2.BackColor = Color.FromArgb(59, 89, 152);
                        btnPor2.Text = "START";
                        txtPor2.Text = "";
                        Por2timer.Stop();
                        PorColorBlack();
                        btnPor1.Enabled = true;

                         
                    }
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }

        private void pnlCanvas_Paint(object sender, PaintEventArgs e)
        {

        }
        //Back End Por1 End Process
        public void BackEndPOR1End()
        {
            try
            {
                if (txtPor1.Text == string.Empty)

                {
                    count = 1;
                   
                    btnPor1.BackColor = Color.FromArgb(59, 89, 152);
                    btnPor1.Text = "START";
                    btnPor2.Enabled = true;
                    //Rotation Stop             
                    Por1timer.Stop(); // commented by jitu on 04-FEB-25
                    PorColorBlack();

                }
            }
            catch (Exception ex)
            { 
                MessageBox.Show(ex.Message);
            }
        }
        public void BackEndPOR2End()
        {
            try
            {
                if (txtPor2.Text == string.Empty)
                {
                    count = 1;
                    
                    //OPC.SendTCPData("L1_POR_COMPL");
                    btnPor2.BackColor = Color.FromArgb(59, 89, 152);
                    btnPor2.Text = "START";

                    Por2timer.Stop();
                    PorColorBlack();
                    btnPor1.Enabled = true;
                }
            }
            catch (Exception ex) 
            {
                MessageBox.Show(ex.Message);
            }
        }
        public void RecoilCoilId()
        {
            try
            {
                Db.DatabaseConnect();
                // string s = "select COIL_ID from T_EVENT_TRACKING Where ID_APP_TAG_EVENT='L1_COIL_EXT' ";
                string s = "select COIL_ID from T_EVENT_TRACKING Where ID_APP_TAG_EVENT='RECOILER' ";
                OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
                DataTable dtRecoile = new DataTable();
                da.Fill(dtRecoile);
                Db.ConClose();
                if (dtRecoile.Rows.Count > 0)
                {
                   if (dtRecoile.Rows[0][0] != DBNull.Value)
                    {
                        textBoxRecoiler1.Text = dtRecoile.Rows[0][0].ToString();
                    }
                    else
                    {
                        txtRecoiller.Text = "";
                    }
                }
                RecoilerSaddleFill();
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }

        public void RecoilerSaddleFill()
        {
            try
            {
                Db.DatabaseConnect();
                // string s = "select COIL_ID from T_EVENT_TRACKING Where ID_APP_TAG_EVENT='L1_COIL_EXT' ";
                string s = "select TCIP_PRODUCT_COIL from T_COL_COIL_INFO_PDO ORDER BY  TCIP_CIL_END_TIME DESC FETCH FIRST 4 ROWS ONLY ";
                OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
                DataTable dtRecoile = new DataTable();
                da.Fill(dtRecoile);
                Db.ConClose();
                if (dtRecoile.Rows.Count > 3)
                {
                    //    if (dtRecoile.Rows[0][0] != DBNull.Value)
                    //{
                    textBoxRecoiler1.Text = dtRecoile.Rows[0]["TCIP_PRODUCT_COIL"].ToString();
                    textBox20.Text = dtRecoile.Rows[1]["TCIP_PRODUCT_COIL"].ToString();
                    textBox21.Text = dtRecoile.Rows[2]["TCIP_PRODUCT_COIL"].ToString();
                    textBox22.Text = dtRecoile.Rows[3]["TCIP_PRODUCT_COIL"].ToString();
                    //}
                    //else
                    //{
                    //    txtRecoiller.Text = "";
                    //}
                }
              
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }


        }




        public void ColorTllT0BR6()
        {
            pict12.BackColor = Color.Red;
            pict13.BackColor = Color.Red;
            pict14.BackColor = Color.Red;
            pict15.BackColor = Color.Red;
            pict16.BackColor = Color.Red;
            pict17.BackColor = Color.Red;
            pict18.BackColor = Color.Red;
            pict19.BackColor = Color.Red;
            pict20.BackColor = Color.Red;
            pict21.BackColor = Color.Red;
            pict22.BackColor = Color.Red;
            pict23.BackColor = Color.Red;
            pict24.BackColor = Color.Red;
            pict25.BackColor = Color.Red;
            pict26.BackColor = Color.Red;
            pict27.BackColor = Color.Red;
            pict28.BackColor = Color.Red;

        }
        public void ColorBr6ToStr6()
        {
            diagonal5.Invalidate();
            diagonal5.BackLineColor = Color.Red;
            pict29.BackColor = Color.Red;
            pict30.BackColor = Color.Red;
            pict31.BackColor = Color.Red;
            pict32.BackColor = Color.Red;
            pict33.BackColor = Color.Red;
        }
        public void ColorStr6ToBr9()
        {
            diagonalSep3.Invalidate();
            diagonalSep3.backLineColorSep = Color.Red;
            pict34.BackColor = Color.Red;
            pict35.BackColor = Color.Red;
            pict36.BackColor = Color.Red;
            pict37.BackColor = Color.Red;
            pict38.BackColor = Color.Red;
            diagonalSep4.Invalidate();
            diagonalSep4.backLineColorSep = Color.Red;
            pict39.BackColor = Color.Red;
            pict40.BackColor = Color.Red;
            diagonalSep5.Invalidate();
            diagonalSep5.backLineColorSep = Color.Red;
            diagonal6.Invalidate();
            diagonal6.BackLineColor = Color.Red;
            pict41.BackColor = Color.Red;
            diagonalSep6.Invalidate();
            diagonalSep6.backLineColorSep = Color.Red;
            pict42.BackColor = Color.Red;
            pict43.BackColor = Color.Red;
            pict44.BackColor = Color.Red;
        }
        public void ColorBr9ToRecoil()
        {
            pict45.BackColor = Color.Red;
            diagonal7.Invalidate();
            diagonal7.BackLineColor = Color.Red;
        }
        public void AutoPor1ColorProcessig()
        {
            try
            {
                Db.DatabaseConnect();
                string s = "select ID_APP_TAG_EVENT,COIL_ID from T_EVENT_TRACKING  ";
                OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
                DataTable dtTrack = new DataTable();
                da.Fill(dtTrack);
                Db.ConClose();
                if (dtTrack.Rows.Count > 0)
                {
                    if (btnPor1.Text == "Processing")
                    {
                        //UPTO TLL
                        if (dtTrack.Rows[1][1].ToString() == txtPor1.Text)
                        {
                           
                                diagonal1.Invalidate();
                                diagonal1.BackLineColor = Color.Red;
                           
                                pict1.BackColor = Color.Red;
                           
                                diagonalSep1.Invalidate();
                                diagonalSep1.backLineColorSep = Color.Red;
                           
                                pict3.BackColor = Color.Red;                          
                                //pict4.BackColor = Color.Red;
                           
                                diagonal3.Invalidate();
                                diagonal3.BackLineColor = Color.Red;
                          
                                pict5.BackColor = Color.Red;                            
                                pict6.BackColor = Color.Red;
                            
                                diagonal4.Invalidate();
                                diagonal4.BackLineColor = Color.Red;
                           
                                pict7.BackColor = Color.Red;
                           
                                diagonalSep2.Invalidate();
                                diagonalSep2.backLineColorSep = Color.Red;
                           
                                pict8.BackColor = Color.Red;                           
                                pict9.BackColor = Color.Red;                           
                                pict10.BackColor = Color.Red;                          
                                pict11.BackColor = Color.Red;
                            
                        }
                        //UPTO TLL TO BR6
                        if (dtTrack.Rows[2][1].ToString() == txtPor1.Text)
                        {
                            ColorTllT0BR6();
                        }
                        //UPTO BR6 TO  ST6
                        if (dtTrack.Rows[3][1].ToString() == txtPor1.Text)
                        {
                            ColorBr6ToStr6();
                        }
                        //UPTO ST6 TO BR9
                        if (dtTrack.Rows[4][1].ToString() == txtPor1.Text)
                        { 
                            ColorStr6ToBr9();
                            btnPor1End.Enabled = true;
                        }
                        //UPTO Br9 RECOILE
                        if (dtTrack.Rows[5][1].ToString() == txtPor1.Text)
                        {
                            ColorBr9ToRecoil();
                           
                        }
                        if (btnPor1.Text == "START")
                        {
                            PorColorBlack();
                        }
                        else
                        {
                            
                        }
                        
                    }
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        public void AutoPor2ColorProcessig()
        {
            try
            {
                Db.DatabaseConnect();
                string s = "select ID_APP_TAG_EVENT,COIL_ID from T_EVENT_TRACKING  ";
                OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
                DataTable dtTrack = new DataTable();
                da.Fill(dtTrack);
                Db.ConClose();
                if (dtTrack.Rows.Count > 0)
                {
                    if (btnPor2.Text == "Processing")
                    {
                        //UPTO POR2 TO TLL
                        if (dtTrack.Rows[1][1].ToString() == txtPor2.Text)
                        {

                          //  diagonal2.Invalidate();
                          //  diagonal2.BackLineColor = Color.Red;
                          //  pict2.BackColor = Color.Red;                            
                            pict3.BackColor = Color.Red;
                         //   pict4.BackColor = Color.Red;
                            diagonal3.Invalidate();
                            diagonal3.BackLineColor = Color.Red;
                            pict5.BackColor = Color.Red;
                            pict6.BackColor = Color.Red;
                            diagonal4.Invalidate();
                            diagonal4.BackLineColor = Color.Red;
                            pict7.BackColor = Color.Red;
                            diagonalSep2.Invalidate();
                            diagonalSep2.backLineColorSep = Color.Red;
                            pict8.BackColor = Color.Red;
                            pict9.BackColor = Color.Red;
                            pict10.BackColor = Color.Red;
                            pict11.BackColor = Color.Red;

                        }
                        //UPTO TLL TO BR6
                        if (dtTrack.Rows[2][1].ToString() == txtPor2.Text)
                        {
                            ColorTllT0BR6();
                        }
                        //UPTO BR6 TO  ST6
                        if (dtTrack.Rows[3][1].ToString() == txtPor2.Text)
                        {
                            ColorBr6ToStr6();
                        }
                        //UPTO ST6 TO BR9
                        if (dtTrack.Rows[4][1].ToString() == txtPor2.Text)
                        {
                            ColorStr6ToBr9();
                            btnEnd.Enabled = true;
                        }
                        //UPTO Br9 RECOILE
                        if (dtTrack.Rows[5][1].ToString() == txtPor2.Text)
                        {
                            ColorBr9ToRecoil();
                           
                        }
                        if (btnPor2.Text == "START")
                        {
                            PorColorBlack();
                        }
                        else
                        {

                        }

                    }
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
        }
        //for Auto Processing 
        private void timerText_Tick(object sender, EventArgs e)
        {
            if (Db.DatabaseConnectCheck())
            {
                LoadTextBoxData();
                //FOR Auto Process compare evnt tracking table for Backend
                AutoProcessingPor();
                //FOR Auto Process when recoil is complete
                RecoilCoilId();
                //Back End Por1 End Process
                //Commented by jitu on 22/05/2025
               // BackEndPOR1End();
                //Back End Por2 End Process

                //Commented by jitu on 22/05/2025
             //   BackEndPOR2End();
                AutoPor1ColorProcessig();
                AutoPor2ColorProcessig();
                //For Mill Actual Speed
                MillActualValue();
                EtryExitAccumulator();
                FetchAllTextValues();
                txtTimePor1Interval.Text = Por1timer.Interval.ToString();
            }

        }

        public void FetchAllTextValues()
        {
            try
            {
                Db.DatabaseConnect();
                string s = "SELECT CHEM_DRYER_TEMP_ACT, FIN_OVN_TEMP_Z1_ACT, FIN_OVN_TEMP_Z2_ACT, FIN_OVN_TEMP_Z3_ACT, FIN_OVN_TEMP_Z4_ACT," +
                           " PRIME_OVN_TEMP_Z1_ACT, PRIME_OVN_TEMP_Z2_ACT, PRIME_OVN_TEMP_Z3_ACT, TLL_TENSION_ACT, BRUSH_TANK_TEMP," +
                           " BRUSH_TANK_CONDUCTIVITY, ALKALI_TANK_WATER_LVL, HOT_AIR_DRY_1_TEMP_SCL, HOT_AIR_DRY_2_TEMP_SCL, HOT_AIR_DRY_3_TEMP_SCL " +
                           " FROM t_periodic_value_log WHERE DATE_TIME = (SELECT MAX(DATE_TIME) FROM T_PERIODIC_VALUE_LOG)";
                OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
                DataTable dtTrack = new DataTable();
                da.Fill(dtTrack);
                Db.ConClose();

                if (dtTrack.Rows.Count > 0)
                {
                    // Assign values to textboxes
                    textBoxChemDryTemp.Text = dtTrack.Rows[0]["CHEM_DRYER_TEMP_ACT"].ToString();
                    textBoxFOZ1.Text = dtTrack.Rows[0]["FIN_OVN_TEMP_Z1_ACT"].ToString();
                    textBoxFOZ2.Text = dtTrack.Rows[0]["FIN_OVN_TEMP_Z2_ACT"].ToString();
                    textBoxFOZ3.Text = dtTrack.Rows[0]["FIN_OVN_TEMP_Z3_ACT"].ToString();
                    textBoxFOZ4.Text = dtTrack.Rows[0]["FIN_OVN_TEMP_Z4_ACT"].ToString();
                    textBoxPOZ1.Text = dtTrack.Rows[0]["PRIME_OVN_TEMP_Z1_ACT"].ToString();
                    textBoxPOZ2.Text = dtTrack.Rows[0]["PRIME_OVN_TEMP_Z2_ACT"].ToString();
                    textBoxPOZ3.Text = dtTrack.Rows[0]["PRIME_OVN_TEMP_Z3_ACT"].ToString();
                    textBoxTLLTEN.Text = dtTrack.Rows[0]["TLL_TENSION_ACT"].ToString();
                    textBoxBrushTankTemp.Text = dtTrack.Rows[0]["BRUSH_TANK_TEMP"].ToString();
                    textBoxBrushTankCond.Text = dtTrack.Rows[0]["BRUSH_TANK_CONDUCTIVITY"].ToString();
                    textBoxAlkaliLevel.Text = dtTrack.Rows[0]["ALKALI_TANK_WATER_LVL"].ToString();
                    textBoxblower1.Text = dtTrack.Rows[0]["HOT_AIR_DRY_1_TEMP_SCL"].ToString();
                    textBoxblower2.Text = dtTrack.Rows[0]["HOT_AIR_DRY_2_TEMP_SCL"].ToString();
                    textBoxblower3.Text = dtTrack.Rows[0]["HOT_AIR_DRY_3_TEMP_SCL"].ToString();
                }
                else
                {
                    MessageBox.Show("No data found!");
                }

            }
            catch (Exception ex) { }
           

        }

       

        private void Recoiler1_Timer_Tick(object sender, EventArgs e)
        {
            Recoiler1Rotation();
        }

        private void Recoiler2_Timer_Tick(object sender, EventArgs e)
        {
            Recoiler2Rotation();
        }

        private void timer1_Tick(object sender, EventArgs e)
        {
            txtTimerStatus.Text = (Convert.ToInt32(txtTimerStatus.Text) + 1).ToString();
           POR1Rotation();
        }
    }
}
