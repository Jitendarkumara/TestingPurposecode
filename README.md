using System;
using System.Data;
using System.Drawing;
using System.Windows.Forms;
using Oracle.ManagedDataAccess.Client; // Ensure you have the correct Oracle library

public partial class Form1 : Form
{
    public Form1()
    {
        InitializeComponent();
        this.Resize += new EventHandler(Form1_Resize); // Attach Resize event
    }

    public void LoadCoilTempGrid()
    {
        try
        {
            Db.DatabaseConnect();

            string query = "SELECT seq_No, coil_id, TOC FROM T_COIL_LOC_TEM";
            OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
            DataTable DtPDI = new DataTable();
            da.Fill(DtPDI);

            // Reset DataGridView before adding new data
            dataGridView2.Columns.Clear();
            dataGridView2.AutoGenerateColumns = false;
            dataGridView2.DataSource = null;
            dataGridView2.AllowUserToAddRows = false;
            dataGridView2.DataSource = DtPDI;

            // Set auto size properties for better UI
            dataGridView2.AutoSizeRowsMode = DataGridViewAutoSizeRowsMode.AllCells;
            dataGridView2.AutoSizeColumnsMode = DataGridViewAutoSizeColumnsMode.Fill;

            // Add columns dynamically
            DataGridViewTextBoxColumn col1 = new DataGridViewTextBoxColumn
            {
                DataPropertyName = "seq_No",
                HeaderText = "Sequence No.",
                Name = "Seq",
                ReadOnly = true
            };
            dataGridView2.Columns.Add(col1);

            DataGridViewTextBoxColumn col2 = new DataGridViewTextBoxColumn
            {
                DataPropertyName = "Coil_id",
                HeaderText = "Coil ID",
                Name = "Coil_ID",
                ReadOnly = true
            };
            dataGridView2.Columns.Add(col2);

            DataGridViewTextBoxColumn col3 = new DataGridViewTextBoxColumn
            {
                DataPropertyName = "TOC",
                HeaderText = "Date Time",
                Name = "dtTime",
                ReadOnly = true
            };
            dataGridView2.Columns.Add(col3);

            // Add Delete Button Column
            DataGridViewButtonColumn btnDelete = new DataGridViewButtonColumn
            {
                HeaderText = "Action",
                Name = "Delete",
                FlatStyle = FlatStyle.Popup,
                Text = "Delete",
                UseColumnTextForButtonValue = true // Ensures button text is displayed
            };
            dataGridView2.Columns.Add(btnDelete);

            // Set button styles for each row
            foreach (DataGridViewRow row in dataGridView2.Rows)
            {
                if (!row.IsNewRow)
                {
                    row.Cells["Delete"].Style.BackColor = Color.AliceBlue;
                    row.Cells["Delete"].Style.ForeColor = Color.Red;
                }
            }

            // Ensure the last empty row is removed
            if (dataGridView2.Rows.Count > 0 && dataGridView2.Rows[dataGridView2.Rows.Count - 1].IsNewRow)
            {
                dataGridView2.Rows.RemoveAt(dataGridView2.Rows.Count - 1);
            }

            // Adjust DataGridView height dynamically
            AdjustGridHeight();

            Db.ConClose();
        }
        catch (Exception ex)
        {
            MessageBox.Show("Error loading data: " + ex.Message);
        }
    }

    private void AdjustGridHeight()
    {
        int rowHeight = dataGridView2.RowTemplate.Height;
        int headerHeight = dataGridView2.ColumnHeadersHeight;
        int totalHeight = headerHeight + (dataGridView2.Rows.Count * rowHeight);

        int maxHeight = this.ClientSize.Height - 50; // Ensure it fits in the form
        int minHeight = 200; // Minimum height to keep visibility

        dataGridView2.Height = Math.Max(Math.Min(totalHeight + 10, maxHeight), minHeight);
    }

    private void Form1_Resize(object sender, EventArgs e)
    {
        AdjustGridHeight();
    }
}