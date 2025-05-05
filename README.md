if (txtDaughterCoil.Text != "" && txtCustomer.Text != "" && txtActThk.Text != "" &&
    txtActWidth.Text != "" && txtRemarks.Text != "" && txtStartTime.Text != "" &&
    txtDuration.Text != "" && txtMotherCoil.Text != "" && txtEndTime.Text != "" &&
    txtHoldReason.Text != "")
{
    DialogResult result = MessageBox.Show("Are you sure you want to proceed with the transfer?", 
                                          "Confirm Transfer", 
                                          MessageBoxButtons.OKCancel, 
                                          MessageBoxIcon.Question);

    if (result == DialogResult.OK)
    {
        // Proceed with the transfer
        CallTransferToMES();
    }
}
else
{
    MessageBox.Show("Mandatory Fields are blank. Please fill all textboxes in Yellow.");
}