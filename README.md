using Oracle.ManagedDataAccess.Client;
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.IO;
using System.Linq;
using System.Runtime.Remoting.Messaging;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace PDO
{
    public partial class Message_Structure_CCL : Form
    { private string coilnumber ;

        DBhelper Db = new DBhelper();
        public Message_Structure_CCL( string coil)
        {
            InitializeComponent();
            coilnumber=coil;
        }

        private void Message_Structure_CCL_Load(object sender, EventArgs e)
        {
            Db.DatabaseConnect();
            loadCMBdata();

            // Check if any coil ID starting with 'D' exists
            string GetMessagequery = "select tc_coil_number, TC_shipping_mark from T_COL_COT_PDI_L3 where TC_Coil_Number='"+coilnumber+"' ";
            using (OracleCommand checkCmd = new OracleCommand(GetMessagequery, Db.Con))
            {
                OracleDataAdapter da = new OracleDataAdapter(GetMessagequery, Db.Con);
                DataTable DtMessage = new DataTable();
                da.Fill(DtMessage);
                if (DtMessage.Rows.Count > 0)
                {
                    txtMessage.Text = DtMessage.Rows[0]["TC_shipping_mark"].ToString() ;
                }
            }
          


        }
        public void loadCMBdata()
        {


            cmbMarkFlag.Items.Clear();
            cmbMarkFlag.Items.Add("N");
            cmbMarkFlag.Items.Add("Y");
            cmbMarkFlag.SelectedIndex = 0;
            cmbMarkLogo.Items.Clear();

            cmbMarkLogo.DisplayMember = "Text";
            cmbMarkLogo.ValueMember = "Value";
            cmbMarkLogo.DataSource = new List<KeyValuePair<string, string>>()
{
    new KeyValuePair<string, string>("0", "No Logo"),
    new KeyValuePair<string, string>("1", "Option 2"),
        new KeyValuePair<string, string>("2", "Durashine"),
    new KeyValuePair<string, string>("3", "Colour Nova")
};

            cmbMarkLogo.SelectedIndex = 0;

            cmbDateTime.Items.Clear();
            cmbDateTime.Items.Add("Y");
            cmbDateTime.Items.Add("N");
            cmbDateTime.SelectedIndex = 0;


        }

        private void txtMessage_TextChanged(object sender, EventArgs e)
        {

        }

        private void btnExportCSV_Click(object sender, EventArgs e)
        {
            
                // Get values from controls
                string value1 = cmbMarkFlag.SelectedItem?.ToString() ?? "";
                string value2 = cmbMarkLogo.SelectedItem?.ToString() ?? "";
            string value3 = cmbDateTime.SelectedItem?.ToString() ?? "";
            string value4 = txtMessage.Text;

            List<string> csvLines = new List<string>
                    {
                // Build CSV line
               
              $"{value1},{value2},{value3},{value4}"
            };

            // Set CSV file path
            string filePath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.Desktop), "output.csv");

            // Write the file (overwrites if exists)
            File.WriteAllLines(filePath, csvLines);

            MessageBox.Show("CSV file created and data written (overwritten if existed).");

            MessageBox.Show("Data saved to CSV!");
             
        }
    }
}
