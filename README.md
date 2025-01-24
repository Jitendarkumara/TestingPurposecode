using System;
using System.Data;
using System.Data.SqlClient;
using System.Windows.Forms;

namespace ShiftFilterApp
{
    public partial class MainForm : Form
    {
        public MainForm()
        {
            InitializeComponent();
        }

        private void FetchData(DateTime selectedDate, string shift)
        {
            string connectionString = "your_connection_string"; // Replace with your database connection string
            string query = @"
                SELECT 
                    tc_coil_number, 
                    TC_WEIGHT, 
                    TC_ID_MESSAGE
                FROM 
                    t_col_cot_pdi_l3
                WHERE 
                    REGEXP_LIKE(TC_ID_MESSAGE, '^\d{4}-\d{2}-\d{2}-\d{2}\.\d{2}\.\d{2}\.\d{6}$')
                    AND TC_COIL_NUMBER NOT IN ('D1000', 'D2000')
                    AND (
                        -- Shift A: 06:00 to 14:00
                        (CAST(TC_ID_MESSAGE AS DATETIME) BETWEEN @StartShift AND @EndShift AND @SelectedShift = 'A')
                        -- Shift B: 14:00 to 22:00
                        OR (CAST(TC_ID_MESSAGE AS DATETIME) BETWEEN @StartShift AND @EndShift AND @SelectedShift = 'B')
                        -- Shift C: 22:00 to 06:00 (next day)
                        OR ((CAST(TC_ID_MESSAGE AS DATETIME) >= @StartShift 
                            OR CAST(TC_ID_MESSAGE AS DATETIME) < @EndShift) AND @SelectedShift = 'C')
                    );";

            // Dynamically compute the time ranges based on the selected shift and date
            DateTime startShift, endShift;
            switch (shift)
            {
                case "A":
                    startShift = selectedDate.AddHours(6);  // 06:00:00
                    endShift = selectedDate.AddHours(14);  // 14:00:00
                    break;
                case "B":
                    startShift = selectedDate.AddHours(14); // 14:00:00
                    endShift = selectedDate.AddHours(22);  // 22:00:00
                    break;
                case "C":
                    startShift = selectedDate.AddHours(22);        // 22:00:00
                    endShift = selectedDate.AddDays(1).AddHours(6); // Next day 06:00:00
                    break;
                default:
                    MessageBox.Show("Invalid shift!");
                    return;
            }

            try
            {
                using (SqlConnection connection = new SqlConnection(connectionString))
                using (SqlCommand command = new SqlCommand(query, connection))
                {
                    command.Parameters.AddWithValue("@StartShift", startShift);
                    command.Parameters.AddWithValue("@EndShift", endShift);
                    command.Parameters.AddWithValue("@SelectedShift", shift);

                    SqlDataAdapter adapter = new SqlDataAdapter(command);
                    DataTable dataTable = new DataTable();
                    adapter.Fill(dataTable);

                    // Display the results in a DataGridView
                    dataGridView1.DataSource = dataTable;
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error: " + ex.Message);
            }
        }

        private void btnFetchData_Click(object sender, EventArgs e)
        {
            // Get the user-selected date and shift
            DateTime selectedDate = dateTimePicker1.Value.Date; // Selected date from DateTimePicker
            string selectedShift = comboBoxShift.SelectedItem?.ToString(); // Selected shift from ComboBox

            if (string.IsNullOrEmpty(selectedShift))
            {
                MessageBox.Show("Please select a shift!");
                return;
            }

            // Fetch data for the selected date and shift
            FetchData(selectedDate, selectedShift);
        }
    }
}