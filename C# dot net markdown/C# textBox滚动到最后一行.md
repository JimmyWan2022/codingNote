## text box滚动到最后一行

```c#
private void textBox1_TextChanged(object sender, EventArgs e)
        {

            if (textBox1.Text.Length > 0)           //防止手动清空时，为0，后面textBox1.Text.Length - 1时出错。
            {
                textBox1.SelectionStart = textBox1.Text.Length;
                textBox1.ScrollToCaret();//将控件内容滚动到当前插入符号位置
            }
        }

```

