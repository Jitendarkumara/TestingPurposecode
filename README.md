   private void timer2_Tick(object sender, EventArgs e)
   {
       LoadPdoGridview();
   }
    private void LoadPdoGridview()
 {
     try
     {
         
         Db.DatabaseConnect();
         string s = "SELECT TCIP_PRODUCT_COIL,TCIP_CIL_END_TIME  FROM T_COL_COIL_INFO_PDO   order by  TCIP_CIL_END_TIME desc";
         OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
         DtPDO = new DataTable();
         da.Fill(DtPDO);
       
         dgvPDO.DataSource = DtPDO;
         dgvPDO.AutoGenerateColumns = false;
         // here we are checking new coil if new coil generate then sticker will Auto dissplay
         if (Lbl_coil.Text == "Coil")
         {
             Lbl_coil.Text = DtPDO.Rows[0][0].ToString();
             //printSticker();
         }
         else
         {
             if (Lbl_coil.Text != DtPDO.Rows[0][0].ToString())
             {
                 Lbl_coil.Text = DtPDO.Rows[0][0].ToString();
                 printSticker();
               
             }
            
         }

         Db.ConClose();
         //dgvCoilDetails.RowHeadersDefaultCellStyle.SelectionBackColor = Color.Red;
         dgvPDO.RowsDefaultCellStyle.BackColor = Color.WhiteSmoke;
         dgvPDO.AlternatingRowsDefaultCellStyle.BackColor = Color.LightGray;
     }
     catch (Exception ex)
     {
         MessageBox.Show(ex.Message);
     }
     public class DBhelper
{
    public OracleConnection Con;
    public string ConnectioString = System.Configuration.ConfigurationManager.ConnectionStrings["ServiceURL"].ToString();
   
    // Define an event to notify when an error occurs
    public event Action<string> OnDatabaseError;
   MasterPage MasterPage1 = new MasterPage();
    public async void  DatabaseConnect()
    {
        try
        {
            Con = new OracleConnection(ConnectioString);
            Con.Open();
        }
        catch (Exception ex)
        {
            if (ex.Message == "Connection request timed out")
            {
                MasterPage1.AutoPopTimer.Stop();
            }

            // Trigger the event instead of showing a message box
            // OnDatabaseError?.Invoke(ex.Message);
        }
    }
    }
