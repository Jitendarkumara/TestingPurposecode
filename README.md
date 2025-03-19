public partial class Form1 : Form
{
    private DBhelper Db;

    public Form1()
    {
        InitializeComponent();
        
        // Pass the timer instance to DBhelper
        Db = new DBhelper(timer2);
    }

    private async void timer2_Tick(object sender, EventArgs e)
    {
        await LoadPdoGridviewAsync();
    }

    private async Task LoadPdoGridviewAsync()
    {
        try
        {
            await Db.DatabaseConnectAsync(); // Wait for database connection

            string query = "SELECT TCIP_PRODUCT_COIL, TCIP_CIL_END_TIME FROM T_COL_COIL_INFO_PDO ORDER BY TCIP_CIL_END_TIME DESC";
            OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
            DataTable DtPDO = new DataTable();
            da.Fill(DtPDO);

            dgvPDO.DataSource = DtPDO;
            dgvPDO.AutoGenerateColumns = false;

            if (Lbl_coil.Text == "Coil")
            {
                Lbl_coil.Text = DtPDO.Rows[0][0].ToString();
            }
            else
            {
                if (Lbl_coil.Text != DtPDO.Rows[0][0].ToString())
                {
                    Lbl_coil.Text = DtPDO.Rows[0][0].ToString();
                    printSticker();
                }
            }

            dgvPDO.RowsDefaultCellStyle.BackColor = Color.WhiteSmoke;
            dgvPDO.AlternatingRowsDefaultCellStyle.BackColor = Color.LightGray;
        }
        catch (Exception ex)
        {
            MessageBox.Show(ex.Message, "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
        }
    }

    private void btnRestartTimer_Click(object sender, EventArgs e)
    {
        timer2.Start(); // Restart the timer manually
        MessageBox.Show("Timer restarted.", "Info", MessageBoxButtons.OK, MessageBoxIcon.Information);
    }
}