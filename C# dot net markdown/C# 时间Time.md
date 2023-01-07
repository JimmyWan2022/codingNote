

获取当前时间 System.DateTime.Now

```c#
 private void dateTimePicker1_ValueChanged(object sender, EventArgs e)
        {
            DateTime dataTime1 = Convert.ToDateTime(dateTimePicker1.Text);
            DateTime currentTime = System.DateTime.Now;
            if (DateTime.Compare(dataTime1, currentTime) < 0)
            {
                //DateTime类型的变量，增加时间
                dataTime1 = currentTime.AddMinutes(1);
                dateTimePicker1.Value = dataTime1;
            }
        }
```



```
DateTime dataTime = DateTime.Now;
//string mydate = string.Format("{0:D4}{1:D2}{2:D2}", dataTime.Year, dataTime.Month, dataTime.Day);
string mydate = string.Format("{0:D4}年{1:D2}月{2:D2}日", dataTime.Year, dataTime.Month, dataTime.Day);
```

将字符串转DateTime

```
string value = "";
dict.TryGetValue("TIME", out value);
var loopFinishedTime = Convert.ToDateTime(value);
```

