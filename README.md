string insertQuery = "INSERT INTO t_ccl_update_status(COIL_ID, UPDATED_BY, DATE_TIME, TABLE_MODIFIED, REMARK) VALUES(:COIL_ID, :Updated_by, :TIME, :Table, :Remark)";

using (OracleCommand cmdInsert = new OracleCommand(insertQuery, Db.Con))
{
    cmdInsert.CommandType = CommandType.Text;
    cmdInsert.Parameters.Add(":COIL_ID", OracleDbType.Varchar2).Value = coilId;
    cmdInsert.Parameters.Add(":Updated_by", OracleDbType.Varchar2).Value = updatedBy;
    cmdInsert.Parameters.Add(":TIME", OracleDbType.TimeStamp).Value = DateTime.Now;
    cmdInsert.Parameters.Add(":Table", OracleDbType.Varchar2).Value = "T_COIL_TRACKING";
    cmdInsert.Parameters.Add(":Remark", OracleDbType.Varchar2).Value = remark;

    int rowInserted = cmdInsert.ExecuteNonQuery();
    if (rowInserted > 0)
    {
        MessageBox.Show("Record updated successfully.");
    }
}