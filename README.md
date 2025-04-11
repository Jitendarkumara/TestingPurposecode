 if (dataGridViewPDORPT.Rows.Count > 0)
 {
     pdocount = dataGridViewPDORPT.Rows.Count;
     coilnumTxtPdo.Text= (pdocount-1).ToString();
     foreach (DataGridViewRow row in dataGridViewPDORPT.Rows)
     {
         if (row.Cells[2].Value != null && decimal.TryParse(row.Cells[2].Value.ToString(), out decimal weight) && row.Cells[4].Value=="Y")
         {
             pdocweight += weight;
             pdocNotNullWeightCount++;
         }
     }
     coilnumGrtxt.Text=pdocNotNullWeightCount.ToString();
     coilwtGRdone.Text=pdocweight.ToString();
 }
