

```c#
 #region 按钮初始化
        /// <summary>
        /// 扫描串口
        /// </summary>
        public void InitializePorts()
        {

            string[] port_names = SerialPort.GetPortNames();
            string last_name = "";

            comboBox1.Items.Clear();//清除数据
            if (port_names == null)
            {
                MessageBox.Show("本机没有串口！", "Error");
                return;
            }
            foreach (string s in System.IO.Ports.SerialPort.GetPortNames())
            {
                //获取有多少个COM口就添加进COMBOX项目列表  
                comboBox1.Items.Add(s);
                last_name = s;//保存最新的一个
            }
            comboBox1.Text = last_name;//显示最新的一个串口
            //comboBox1.comport_number = last_name;//赋值变量

        }
```

