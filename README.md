   private void btnSavepdo_Click(object sender, EventArgs e)
   {
     
           try
           {
               DateTime START = dateTimeStartTime.Value;
               DateTime END = dateTimeEndTime.Value;

               if (END <= DateTime.Now)
               {
               if (textDuration.Text != "0" && END > START)
               {
                   if (textProductCoilId.Text != "")
                   {
                       // Connect to the database
                       Db.DatabaseConnect(); // Ensure that you have a valid database connection

                       // Check if the product coil ID already exists
                       string checkQuery = "SELECT COUNT(*) FROM T_PDO_INFO WHERE CGD_ID_COIL = :productCoilId";

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
                       string insertQuery = @"INSERT INTO T_PDO_INFO
(CGD_MS_ACTL,CGD_MS_CAL, CGD_ID_COIL, CGD_ID_COIL_MTHR, CGD_WIDTH,
 CGD_TS_START, CGD_TS_END, CGD_WORK_DUR,
 CGD_LN_COIL,CGD_ACT_LENGTH, CGD_IDIA, CGD_ODIA,
 CGD_HOLD_REQ, CGD_CD_RSN_HOLD, CGD_ACT_THICKNESS,
 REMARKS,CGD_MK_CUSTOMER,STATUS)
VALUES
(:actualWeight,:actualWeight, :productCoilId, :inputCoilId, :coilWidth,
 :startTime, :endTime, :duration,
 :coilLength,:coilLength, :innerDiameter, :outerDiameter,
 :holdRequest, :holdReason, :actualThickness,
 :remarks,:customer,:Status)";

                       // Create OracleCommand for insertion
                       using (OracleCommand cmd = new OracleCommand(insertQuery, Db.Con))
                       {
                           // Bind values to the query from the textboxes
                           cmd.Parameters.Add(new OracleParameter("actualWeight", textActWeight.Text));
                           cmd.Parameters.Add(new OracleParameter("productCoilId", textProductCoilId.Text.ToUpper()));
                           cmd.Parameters.Add(new OracleParameter("inputCoilId", textInputCoilId.Text.ToUpper()));
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
                           cmd.Parameters.Add(new OracleParameter("customer", lblCustomer.Text));
                           cmd.Parameters.Add(new OracleParameter("Status", "Manual"));


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
                       MessageBox.Show("Product Coil Id can not be blank");
                   }
               }
               else
               {
                   MessageBox.Show("Time duration can not be 0 or less than 0");
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
