using System;
using System.Data;
using System.Drawing;
using System.Windows.Forms;
using Oracle.ManagedDataAccess.Client;

namespace CoilApp
{
    public partial class CoilForm : Form
    {
        public CoilForm()
        {
            InitializeComponent();
            LoadCoilTempGrid();
        }

        // 🟩 Load Data into DataGridView
        public void LoadCoilTempGrid()
        {
            try
            {
                Db.DatabaseConnect();

                string query = "SELECT seq_No, coil_id, TOC FROM T_COIL_LOC_TEM";
                OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
                DataTable dt = new DataTable();
                da.Fill(dt);

                // Reset grid
                dataGridView2.Columns.Clear();
                dataGridView2.AutoGenerateColumns = false;
                dataGridView2.DataSource = null;
                dataGridView2.AllowUserToAddRows = false;
                dataGridView2.DataSource = dt;

                // Grid styling
                dataGridView2.AutoSizeRowsMode = DataGridViewAutoSizeRowsMode.AllCells;
                dataGridView2.AutoSizeColumnsMode = DataGridViewAutoSizeColumnsMode.Fill;
                dataGridView2.DefaultCellStyle.Font = new Font("Segoe UI", 10);

                // Sequence No. (Editable)
                DataGridViewTextBoxColumn colSeq = new DataGridViewTextBoxColumn
                {
                    DataPropertyName = "seq_No",
                    HeaderText = "Sequence No.",
                    Name = "Seq",
                    ReadOnly = false // Allow editing
                };
                dataGridView2.Columns.Add(colSeq);

                // Coil ID (Read-only)
                DataGridViewTextBoxColumn colCoil = new DataGridViewTextBoxColumn
                {
                    DataPropertyName = "coil_id",
                    HeaderText = "Coil ID",
                    Name = "Coil_ID",
                    ReadOnly = true
                };
                dataGridView2.Columns.Add(colCoil);

                // TOC (Read-only)
                DataGridViewTextBoxColumn colTOC = new DataGridViewTextBoxColumn
                {
                    DataPropertyName = "TOC",
                    HeaderText = "Date Time",
                    Name = "dtTime",
                    ReadOnly = true
                };
                dataGridView2.Columns.Add(colTOC);

                // 🔁 Update Sequence button
                DataGridViewButtonColumn btnUpdateSeq = new DataGridViewButtonColumn
                {
                    HeaderText = "Update Sequence",
                    Name = "UpdateSeq",
                    FlatStyle = FlatStyle.Popup,
                    Text = "Update Seq",
                    UseColumnTextForButtonValue = true
                };
                dataGridView2.Columns.Add(btnUpdateSeq);

                // 🗑 Delete button
                DataGridViewButtonColumn btnDelete = new DataGridViewButtonColumn
                {
                    HeaderText = "Delete Action",
                    Name = "Delete",
                    FlatStyle = FlatStyle.Popup,
                    Text = "Delete",
                    UseColumnTextForButtonValue = true
                };
                dataGridView2.Columns.Add(btnDelete);

                Db.ConClose();
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error loading data: " + ex.Message);
            }
        }

        // 🧭 Handle button clicks
        private void dataGridView2_CellContentClick(object sender, DataGridViewCellEventArgs e)
        {
            if (e.RowIndex < 0) return;

            if (string.IsNullOrWhiteSpace(txtmodified.Text) || string.IsNullOrWhiteSpace(txtRemark.Text))
            {
                MessageBox.Show("Please enter 'Modified By' and 'Remark' before proceeding.");
                return;
            }

            string coilId = dataGridView2.Rows[e.RowIndex].Cells["Coil_ID"].Value.ToString();

            // 🔁 UPDATE SEQUENCE
            if (e.ColumnIndex == dataGridView2.Columns["UpdateSeq"].Index)
            {
                try
                {
                    string seqText = dataGridView2.Rows[e.RowIndex].Cells["Seq"].Value.ToString();

                    if (!int.TryParse(seqText, out int newSeq))
                    {
                        MessageBox.Show("Please enter a valid numeric sequence number.");
                        return;
                    }

                    Db.DatabaseConnect();

                    string updateQuery = "UPDATE T_COIL_LOC_TEM SET seq_no = :newSeq WHERE coil_id = :coilId";
                    using (OracleCommand cmdUpdate = new OracleCommand(updateQuery, Db.Con))
                    {
                        cmdUpdate.Parameters.Add(":newSeq", OracleDbType.Int32).Value = newSeq;
                        cmdUpdate.Parameters.Add(":coilId", OracleDbType.Varchar2).Value = coilId;
                        cmdUpdate.ExecuteNonQuery();
                    }

                    // Log update
                    InsertAuditLog(coilId, $"SEQ updated to {newSeq}");

                    Db.ConClose();
                    MessageBox.Show($"Sequence number for Coil ID {coilId} updated successfully!", "Updated",
                        MessageBoxButtons.OK, MessageBoxIcon.Information);
                }
                catch (Exception ex)
                {
                    MessageBox.Show("Error updating sequence: " + ex.Message);
                }
            }

            // 🗑 DELETE ROW
            else if (e.ColumnIndex == dataGridView2.Columns["Delete"].Index)
            {
                DialogResult result = MessageBox.Show(
                    $"Are you sure you want to delete Coil ID {coilId}?",
                    "Confirm Delete", MessageBoxButtons.OKCancel, MessageBoxIcon.Warning);

                if (result == DialogResult.OK)
                {
                    try
                    {
                        Db.DatabaseConnect();

                        string deleteQuery = "DELETE FROM T_COIL_LOC_TEM WHERE coil_id = :coilId";
                        using (OracleCommand cmdDelete = new OracleCommand(deleteQuery, Db.Con))
                        {
                            cmdDelete.Parameters.Add(new OracleParameter("coilId", coilId));
                            cmdDelete.ExecuteNonQuery();
                        }

                        // Log delete
                        InsertAuditLog(coilId, "DELETE");

                        Db.ConClose();

                        dataGridView2.Rows.RemoveAt(e.RowIndex);
                        MessageBox.Show("Record deleted successfully!", "Deleted",
                            MessageBoxButtons.OK, MessageBoxIcon.Information);
                    }
                    catch (Exception ex)
                    {
                        MessageBox.Show("Error deleting data: " + ex.Message);
                    }
                }
            }
        }

        // 🧾 Common Audit Log Insert Function
        private void InsertAuditLog(string coilId, string remarkAction)
        {
            string insertQuery = @"INSERT INTO t_ccl_update_status 
                (COIL_ID, UPDATED_BY, DATE_TIME, TABLE_MODIFIED, REMARK)
                VALUES (:COIL_ID, :UPDATED_BY, :DATE_TIME, :TABLE_MODIFIED, :REMARK)";

            using (OracleCommand cmdInsert = new OracleCommand(insertQuery, Db.Con))
            {
                cmdInsert.Parameters.Add(":COIL_ID", OracleDbType.Varchar2).Value = coilId;
                cmdInsert.Parameters.Add(":UPDATED_BY", OracleDbType.Varchar2).Value = txtmodified.Text;
                cmdInsert.Parameters.Add(":DATE_TIME", OracleDbType.TimeStamp).Value = DateTime.Now;
                cmdInsert.Parameters.Add(":TABLE_MODIFIED", OracleDbType.Varchar2).Value = "T_COIL_LOC_TEM";
                cmdInsert.Parameters.Add(":REMARK", OracleDbType.Varchar2).Value = $"{txtRemark.Text} - {remarkAction}";
                cmdInsert.ExecuteNonQuery();
            }
        }
    }
}