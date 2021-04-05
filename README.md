# 210405-tcp-ip
network 수업1
```
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Net.Sockets;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace WindowsFormsApp1
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        delegate void cbAddText(string str);    // 함수의 delegate (함수포인터) 선언

        private void AddText(string str)
        {
            if(tbServer.InvokeRequired)
            {
                cbAddText addText = new cbAddText(AddText);
                Invoke(addText, new object[] { str });
            }
            else
            {
                tbServer.Text += str;
            }
        }

        private void btnComTest_Click(object sender, EventArgs e)
        {
            string src = tbClient.Text;
            Socket sock = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);    // Tcp/IP 통신 기본
            sock.Connect(tbIP.Text, int.Parse(tbPort.Text));
            sock.Send(Encoding.Default.GetBytes(src));
        }

        private void btnStart_Click(object sender, EventArgs e)
        {
            Thread ThreadFunc = new Thread(ServerProcess);
            ThreadFunc.Start();
        }
        TcpListener listener;// = new TcpListener(int.Parse(tbPort.Text));

        private void ServerProcess()
        {
            //while (true)
            {
                listener = new TcpListener(int.Parse(tbPort.Text));
                listener.Start();

                byte[] bArr = new byte[200];
                TcpClient tcp = listener.AcceptTcpClient();
                NetworkStream ns = tcp.GetStream();
                ns.Read(bArr, 0, 200);
                string s = Encoding.Default.GetString(bArr);
                AddText(s);
            }
        }
    }
}
```
