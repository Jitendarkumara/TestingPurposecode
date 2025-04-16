_generalForm = new PDO.General();
_generalForm.TopLevel = false;
_generalForm.AutoScroll = true;
_generalForm.Dock = DockStyle.Fill;
pnlPdo.Controls.Clear();
pnlPdo.Controls.Add(_generalForm);
_generalForm.Show();