using System;
using System.Net.NetworkInformation;
using System.Windows.Forms;

public partial class Form1 : Form
{
    public Form1()
    {
        InitializeComponent();
    }

    private void btnPing_Click(object sender, EventArgs e)
    {
        string ipAddress = "8.8.8.8"; // Change to your target IP
        Ping ping = new Ping();
        
        try
        {
            PingReply reply = ping.Send(ipAddress);
            if (reply.Status == IPStatus.Success)
            {
                MessageBox.Show($"Ping successful! Time: {reply.RoundtripTime} ms", "Ping Result");
            }
            else
            {
                MessageBox.Show($"Ping failed: {reply.Status}", "Ping Result");
            }
        }
        catch (Exception ex)
        {
            MessageBox.Show($"Error: {ex.Message}", "Ping Error");
        }
    }
}