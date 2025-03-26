Oracle.ManagedDataAccess.Client.OracleException
  HResult=0x80004005
  Message=Connection request timed out
  Source=Oracle Data Provider for .NET, Managed Driver
  StackTrace:
   at OracleInternal.ConnectionPool.PoolManager`3.Get(ConnectionString csWithDiffOrNewPwd, Boolean bGetForApp, OracleConnection connRefForCriteria, String affinityInstanceName, Boolean bForceMatch)
   at OracleInternal.ConnectionPool.OraclePoolManager.Get(ConnectionString csWithNewPassword, Boolean bGetForApp, OracleConnection connRefForCriteria, String affinityInstanceName, Boolean bForceMatch)
   at OracleInternal.ConnectionPool.OracleConnectionDispenser`3.Get(ConnectionString cs, PM conPM, ConnectionString pmCS, SecureString securedPassword, SecureString securedProxyPassword, OracleConnection connRefForCriteria)
   at Oracle.ManagedDataAccess.Client.OracleConnection.Open()
   at PDO.DBhelper.DatabaseConnect() in D:\CCL_HMI_Latest_19_03_25\CCL_HMI\DBhelper.cs:line 23

  This exception was originally thrown at this call stack:
    [External Code]
    PDO.DBhelper.DatabaseConnect() in DBhelper.cs

     public class DBhelper
 {
     public OracleConnection Con;
     public string ConnectioString = System.Configuration.ConfigurationManager.ConnectionStrings["ServiceURL"].ToString();
     private System.Windows.Forms.Timer _timer;
     // Define an event to notify when an error occurs
     public event Action<string> OnDatabaseError;
  //  MasterPage MasterPage1 = new MasterPage();
     

     public void DatabaseConnect()
     {
         try
         {
             Con = new OracleConnection(ConnectioString);
             Con.Open(); // Run Open() in a background thread
         }
         catch (Exception ex)
         {
             if (ex.Message.Contains("Connection request timed out"))
             {
                 if (Con != null)
                 {
                     Con.Close();
                    // Con.Dispose();
                 }
                 //MasterPage1.AutoPopTimer.Stop();

                 //MessageBox.Show("Database connection timed out. Timer stopped.", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
             }

           //  OnDatabaseError?.Invoke(ex.Message); // Notify any subscribed forms
         }
     }
 
   
     public void ConClose()
     {
         if (Con != null)
         {
             Con.Close();
             Con.Dispose();
         }
     }
 }

  public void LoadPdiGridView()
 {
     try
     {
         Db.DatabaseConnect();
         string s = "select DISTINCT TC_COIL_NUMBER,TC_WIDTH,TC_ID_MESSAGE FROM T_COL_COT_PDI_L3 order by TC_ID_MESSAGE DESC";
         // string s = "select TC_COIL_NUMBER,TC_WIDTH  FROM T_COL_COT_PDI_L3  order by SEQ_NUM DESC";
         //string s = "select COIL_ID,LOC  FROM T_COIL_LOC order by COIL_ID";
         //string s = " SELECT T_COL_COT_PDI_L3.TC_COIL_NUMBER,T_COIL_LOC.LOC FROM  T_COL_COT_PDI_L3 LEFT JOIN T_COIL_LOC ON T_COL_COT_PDI_L3.TC_COIL_NUMBER = T_COIL_LOC.COIL_ID ORDER BY T_COL_COT_PDI_L3.TC_COIL_NUMBER";
         OracleDataAdapter da = new OracleDataAdapter(s, Db.Con);
         DtPDI = new DataTable();
         da.Fill(DtPDI);
         dgvPDI.DataSource = DtPDI;
         dgvPDI.AutoGenerateColumns = false;
         Db.ConClose();
         dgvPDI.AutoGenerateColumns = false;
         dgvPDI.RowsDefaultCellStyle.BackColor = Color.WhiteSmoke;
         dgvPDI.AlternatingRowsDefaultCellStyle.BackColor = Color.LightGray;
         dgvPDI.DefaultCellStyle.ForeColor = Color.Black;
     }
     catch (Exception ex)
     {
         MessageBox.Show(ex.Message);
     }
 }
