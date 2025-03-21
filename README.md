using System;
using System.Data;
using System.Drawing;
using System.Windows.Forms;
using Oracle.ManagedDataAccess.Client; // Ensure you have Oracle.DataAccess installed

namespace DataGridViewExample
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
            LoadEventTrackGrid(); // Load data on form load
        }

        private void LoadEventTrackGrid()
        {
            try
            {
                Db.DatabaseConnect();
                string query = "SELECT Id_app_tag_Event, Coil_id FROM T_Event_Tracking";

                OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
                DataTable DtPDI = new DataTable();
                da.Fill(DtPDI);

                dataGridView1.AutoGenerateColumns = false;
                dataGridView1.DataSource = DtPDI;

                // Clear existing columns to prevent duplication
                dataGridView1.Columns.Clear();

                // Add columns dynamically with correct mapping
                DataGridViewTextBoxColumn col1 = new DataGridViewTextBoxColumn();
                col1.DataPropertyName = "Id_app_tag_Event"; // Must match column name from database
                col1.HeaderText = "Tag";
                col1.Name = "Tag";
                col1.ReadOnly = true; // Prevent editing primary key
                dataGridView1.Columns.Add(col1);

                DataGridViewTextBoxColumn col2 = new DataGridViewTextBoxColumn();
                col2.DataPropertyName = "Coil_id"; // Must match database column name
                col2.HeaderText = "Coil ID";
                col2.Name = "Coil_ID";
                col2.ReadOnly = true; // Initially read-only
                dataGridView1.Columns.Add(col2);

                // Add Edit/Save Button Column
                DataGridViewButtonColumn btnEditSave = new DataGridViewButtonColumn();
                btnEditSave.HeaderText = "Action";
                btnEditSave.Name = "EditSave";
                btnEditSave.Text = "Edit"; // Initial button text
                btnEditSave.UseColumnTextForButtonValue = false; // Allow dynamic text change
                btnEditSave.DefaultCellStyle.Alignment = DataGridViewContentAlignment.MiddleCenter;
                btnEditSave.FlatStyle = FlatStyle.Popup;
                dataGridView1.Columns.Add(btnEditSave);

                // Initialize button text in each row
                foreach (DataGridViewRow row in dataGridView1.Rows)
                {
                    row.Cells["EditSave"].Value = "Edit";
                }

                // Attach event handler
                dataGridView1.CellContentClick += dataGridView1_CellContentClick;

                Db.ConClose();
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error loading data: " + ex.Message);
            }
        }

        private void dataGridView1_CellContentClick(object sender, DataGridViewCellEventArgs e)
        {
            if (e.RowIndex >= 0 && dataGridView1.Columns[e.ColumnIndex].Name == "EditSave")
            {
                DataGridViewRow row = dataGridView1.Rows[e.RowIndex];
                DataGridViewCell btnCell = row.Cells["EditSave"];

                if (btnCell.Value == null || btnCell.Value.ToString() == "Edit")
                {
                    // Enable editing
                    row.Cells["Coil_ID"].ReadOnly = false;
                    btnCell.Value = "Save"; // Change button text
                    dataGridView1.Refresh(); // Refresh UI
                }
                else if (btnCell.Value.ToString() == "Save")
                {
                    try
                    {
                        // Get updated values
                        string id = row.Cells["Tag"].Value.ToString();
                        string coilId = row.Cells["Coil_ID"].Value.ToString();

                        // Update query
                        string updateQuery = "UPDATE T_Event_Tracking SET Coil_id = :CoilID WHERE Id_app_tag_Event = :Id";

                        Db.DatabaseConnect();
                        using (OracleCommand cmd = new OracleCommand(updateQuery, Db.Con))
                        {
                            cmd.Parameters.Add(new OracleParameter(":CoilID", coilId));
                            cmd.Parameters.Add(new OracleParameter(":Id", id));

                            int rowsAffected = cmd.ExecuteNonQuery();
                            if (rowsAffected > 0)
                            {
                                MessageBox.Show("Record updated successfully.");
                            }
                            else
                            {
                                MessageBox.Show("No record updated.");
                            }
                        }
                        Db.ConClose();

                        // Disable editing and change button text back to Edit
                        row.Cells["Coil_ID"].ReadOnly = true;
                        btnCell.Value = "Edit"; // Change button text back
                        dataGridView1.Refresh(); // Refresh UI
                    }
                    catch (Exception ex)
                    {
                        MessageBox.Show("Error updating record: " + ex.Message);
                    }
                }
            }
        }
    }
}