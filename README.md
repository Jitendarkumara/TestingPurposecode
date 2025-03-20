private void dataGridView1_CellContentClick(object sender, DataGridViewCellEventArgs e)
{
    // Check if "Save" button is clicked
    if (dataGridView1.Columns[e.ColumnIndex] is DataGridViewButtonColumn && e.RowIndex >= 0)
    {
        int id = Convert.ToInt32(dataGridView1.Rows[e.RowIndex].Cells["ID"].Value);
        string name = dataGridView1.Rows[e.RowIndex].Cells["Name"].Value.ToString();
        string designation = dataGridView1.Rows[e.RowIndex].Cells["Designation"].Value.ToString();

        // Call Update method
        UpdateData(id, name, designation);
    }
}

private void UpdateData(int id, string name, string designation)
{
    using (SqlConnection conn = new SqlConnection(connectionString))
    {
        string query = "UPDATE Employees SET Name = @Name, Designation = @Designation WHERE ID = @ID";
        using (SqlCommand cmd = new SqlCommand(query, conn))
        {
            cmd.Parameters.AddWithValue("@ID", id);
            cmd.Parameters.AddWithValue("@Name", name);
            cmd.Parameters.AddWithValue("@Designation", designation);

            conn.Open();
            cmd.ExecuteNonQuery();
            conn.Close();

            MessageBox.Show("Record Updated Successfully!", "Success", MessageBoxButtons.OK, MessageBoxIcon.Information);
        }
    }
}