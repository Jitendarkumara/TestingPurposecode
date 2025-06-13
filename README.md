   private void button1_Click(object sender, EventArgs e)
   {
       if (dateTimeEndTime.Value > DateTime.Now)
        {
           MessageBox.Show("End Time should not greater then current time..");
           return;
           
        }
       //if (string.IsNullOrEmpty(textDuration.Text) || string.IsNullOrEmpty(textProductCoilId.Text))
       //{
       //    MessageBox.Show("Fill required Fields");

       //}
 //  int Coilcount =  CheckTimeDuration(dateTimeStartTime.Value, dateTimeEndTime.Value);
       //if (Coilcount == 0)
       //{
           if (DateTime.Now - DateTime.Parse(dateTimeEndTime.Text) > TimeSpan.FromHours(24))
           {
               MessageBox.Show("GR Time has expired");
           }
           else
           {
               if (textProductCoilId.Text != "")
               {
                   if (textDuration.Text != "")
                   {
                       if (Convert.ToInt32(float.Parse(textDuration.Text)) < 1)
                       {
                           MessageBox.Show("Duration can not be less then 60 second..");

                       }
                       else
                       {
                           try
                           {

                               DateTime START = DateTime.ParseExact(dateTimeStartTime.Text,"dd-MMM-yy hh:mm tt",CultureInfo.InvariantCulture);
                               DateTime END = DateTime.ParseExact(dateTimeEndTime.Text, "dd-MMM-yy hh:mm tt", CultureInfo.InvariantCulture);

                               if (END <= DateTime.Now)
                               {
                                   // Connect to the database
                                   Db.DatabaseConnect(); // Ensure that you have a valid database connection

                                   // Check if the product coil ID already exists
                                   string checkQuery = "SELECT COUNT(*) FROM T_COL_COIL_INFO_PDO WHERE TCIP_PRODUCT_COIL = :productCoilId";

                                   // Create OracleCommand to check existence
                                   using (OracleCommand checkCmd = new OracleCommand(checkQuery, Db.Con))
                                   {
                                       checkCmd.Parameters.Add(new OracleParameter("productCoilId", textProductCoilId.Text));

                                       // Execute the query and get the result
                                       int count = Convert.ToInt32(checkCmd.ExecuteScalar());

                                       if (count > 0)
                                       {
                                           // Product coil ID already exists, show a message
                                           MessageBox.Show("Already exists PDO for this Product Coil ID.");
                                           return; // Exit the method, no insertion
                                       }
                                   }

                                   // SQL Insert Query
                                   string insertQuery = @"INSERT INTO T_COL_COIL_INFO_PDO
(TCPI_ACTUAL_WT, TCIP_PRODUCT_COIL, TCIP_INPUT_COIL, TCPI_PROD_COIL_WDT,
 TCIP_CIL_START_TIME, TCIP_CIL_END_TIME, TCPI_DURATION,
 TCPI_PROD_COIL_LENGTH, TCPI_PROD_COIL_IDIA, TCPI_PROD_COIL_ODIA,
 TCPI_NXPROC_HOLD_REQ, TCPI_NXPROC_HOLD_RES1, TCPI_PROD_COIL_THICK_AFT_PRINT,
 REMARKS,MANUAL_REASON,PDO_FLAG)
VALUES
(:actualWeight, :productCoilId, :inputCoilId, :coilWidth,
 :startTime, :endTime, :duration,
 :coilLength, :innerDiameter, :outerDiameter,
 :holdRequest, :holdReason, :actualThickness,
 :remarks,:MANUAL_REASON,
 :PDO_FLAG)";

                                   // Create OracleCommand for insertion
                                   using (OracleCommand cmd = new OracleCommand(insertQuery, Db.Con))
                                   {
                                       // Bind values to the query from the textboxes
                                       cmd.Parameters.Add(new OracleParameter("actualWeight", textActWeight.Text));
                                       cmd.Parameters.Add(new OracleParameter("productCoilId", textProductCoilId.Text));
                                       cmd.Parameters.Add(new OracleParameter("inputCoilId", textInputCoilId.Text));
                                       cmd.Parameters.Add(new OracleParameter("coilWidth", textInpiutWidth.Text));
                                       cmd.Parameters.Add(new OracleParameter("startTime", dateTimeStartTime.Value)); // DateTimePicker control
                                       cmd.Parameters.Add(new OracleParameter("endTime", dateTimeEndTime.Value)); // DateTimePicker control
                                       cmd.Parameters.Add(new OracleParameter("duration", textDuration.Text));
                                       cmd.Parameters.Add(new OracleParameter("coilLength", textlength.Text));
                                       cmd.Parameters.Add(new OracleParameter("innerDiameter", textInnerDia.Text));
                                       cmd.Parameters.Add(new OracleParameter("outerDiameter", textOdia.Text));
                                       cmd.Parameters.Add(new OracleParameter("holdRequest", CMBHoldRequest.Text));
                                       cmd.Parameters.Add(new OracleParameter("holdReason", textHoldReason.Text));
                                       cmd.Parameters.Add(new OracleParameter("actualThickness", textActThickness.Text));
                                       cmd.Parameters.Add(new OracleParameter("remarks", textRemarks.Text));
                                       cmd.Parameters.Add(new OracleParameter("Manual_Reason", cmbMan_Reason.Text));
                                       cmd.Parameters.Add(new OracleParameter("Flag", 'M'));

                                       // Execute the insert command
                                       int rowsAffected = cmd.ExecuteNonQuery();

                                       // Optionally show a message indicating success
                                       if (rowsAffected > 0)
                                       {
                                           MessageBox.Show("Data inserted successfully!");
                                       }
                                   }
                               }

                               else
                               {
                                   MessageBox.Show("End time should be less than or equal to currrent date time .");
                               }


                           }


                           catch (Exception ex)
                           {
                               // Handle any exceptions that occur
                               MessageBox.Show("Error inserting data: " + ex.Message);
                           }
                           finally
                           {
                               // Close the database connection if necessary
                               Db.ConClose();
                           }
                       }
                   }
                   else
                   {
                       MessageBox.Show("Duration can not be Blank..");

                   }

               }

               else
               {
                   MessageBox.Show("Product coil can not be blank..");
               }
           }
      // }
       
   }
