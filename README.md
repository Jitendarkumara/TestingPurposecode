using System;
using System.Windows.Forms;

namespace DataGridViewExample
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
            InitializeDataGridView();
        }

        private void InitializeDataGridView()
        {
            // Create DataGridView
            DataGridView dataGridViewEvent_track = new DataGridView
            {
                Location = new System.Drawing.Point(25, 32),
                Size = new System.Drawing.Size(400, 250),
                AutoSizeColumnsMode = DataGridViewAutoSizeColumnsMode.Fill,
                AllowUserToAddRows = false,
                AllowUserToDeleteRows = false
            };

            // Add Columns
            dataGridViewEvent_track.Columns.Add("ID", "ID");
            dataGridViewEvent_track.Columns.Add("Name", "Name");

            // Set Column Widths
            dataGridViewEvent_track.Columns[0].Width = 100;
            dataGridViewEvent_track.Columns[1].Width = 200;

            // Make first column ReadOnly
            dataGridViewEvent_track.Columns[0].ReadOnly = true;

            // Add an "Edit" button column
            DataGridViewButtonColumn editButton = new DataGridViewButtonColumn
            {
                HeaderText = "Action",
                Text = "Edit",
                UseColumnTextForButtonValue = true
            };
            dataGridViewEvent_track.Columns.Add(editButton);

            // Add Sample Data
            dataGridViewEvent_track.Rows.Add("1", "John Doe");
            dataGridViewEvent_track.Rows.Add("2", "Jane Smith");

            // Handle Cell Click Event
            dataGridViewEvent_track.CellContentClick += DataGridViewEvent_track_CellContentClick;

            // Add DataGridView to the form
            this.Controls.Add(dataGridViewEvent_track);
        }

        private void DataGridViewEvent_track_CellContentClick(object sender, DataGridViewCellEventArgs e)
        {
            DataGridView grid = sender as DataGridView;

            if (grid.Columns[e.ColumnIndex] is DataGridViewButtonColumn && e.RowIndex >= 0)
            {
                string id = grid.Rows[e.RowIndex].Cells[0].Value.ToString();
                string name = grid.Rows[e.RowIndex].Cells[1].Value.ToString();

                MessageBox.Show($"Editing ID: {id}, Name: {name}", "Edit Record");

                // Here you can open a new form to edit the record
                // Or allow inline editing directly in the DataGridView
            }
        }
    }
}