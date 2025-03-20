 private void UpdateData(string Coilid, string TagName)
 {
     Db.DatabaseConnect();
     string query = "UPDATE T_Event_Tracking set COIL_ID=@  WHERE ID_APP_TAG_EVENT = @ID";
     OracleDataAdapter da = new OracleDataAdapter(query, Db.Con);
     cmd.Parameters.AddWithValue("@Coilid", Coilid);
             cmd.Parameters.AddWithValue("@Name", TagName);
            

             conn.Open();
             cmd.ExecuteNonQuery();
             conn.Close();

             MessageBox.Show("Record Updated Successfully!", "Success", MessageBoxButtons.OK, MessageBoxIcon.Information);
         
     
 }
