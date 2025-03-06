using Oracle.ManagedDataAccess.Client;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace PDO
{
    public class DBhelper
    {

        public OracleConnection Con;
        public string ConnectioString = System.Configuration.ConfigurationManager.ConnectionStrings["ServiceURL"].ToString();
        public void DatabaseConnect()
        {
            try
            {
                Con = new OracleConnection(ConnectioString);
                Con.Open();


            }
            catch (Exception ex)
            {
               // MessageBox.Show(ex.Message);
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
}
