 public void BackEndPOR1End()
 {
     try
     {
         if (txtPor1.Text == string.Empty)

         {
             count = 1;
            
             btnPor1.BackColor = Color.FromArgb(59, 89, 152);
             btnPor1.Text = "START";
             btnPor2.Enabled = true;
             //Rotation Stop             
             Por1timer.Stop(); // commented by jitu on 04-FEB-25
             PorColorBlack();

         }
     }
     catch (Exception ex)
     { 
         MessageBox.Show(ex.Message);
     }
 }
