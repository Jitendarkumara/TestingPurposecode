cmdInsert.Parameters.Add(":COIL_ID", OracleDbType.Varchar2).Value = coilId;
cmdInsert.Parameters.Add(":UPDATED_BY", OracleDbType.Varchar2).Value = txtbox_modifiedBy.Text;
cmdInsert.Parameters.Add(":DATE_TIME", OracleDbType.TimeStamp).Value = DateTime.Now;
cmdInsert.Parameters.Add(":TABLE_MODIFIED", OracleDbType.Varchar2).Value = "T_COIL_TRACKING";
cmdInsert.Parameters.Add(":REMARK", OracleDbType.Varchar2).Value = txtbox_Remark.Text;