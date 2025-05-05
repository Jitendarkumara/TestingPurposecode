if (txtDaughterCoil.Text != "" && txtCustomer.Text !="" && txtActThk.Text !="" && txtActWidth.Text!="" && txtRemarks.Text!="" && txtStartTime.Text!="" && txtDuration.Text!= "" && txtMotherCoil.Text!="" && txtEndTime.Text!="" && txtHoldReason.Text!="")
{

    // Proceed with the transfer
    CallTransferToMES();
}
else
{
    MessageBox.Show("Mandatory Fields are blank. Please fill all textboxes in Yellow.");
}
