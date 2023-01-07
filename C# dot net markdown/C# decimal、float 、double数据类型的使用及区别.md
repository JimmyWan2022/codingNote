## .NET(C#)decimal、float 、double数据类型的使用及区别


本文主要介绍.NET(C#)中decimal、float 、double数据类型的使用及它们之前使用上的区别。

**1、decimal、float 、double的区别**

`float`和`double`是浮动二进制点类型。换句话说，它们代表了这样的数字：

```
10001.10010110011
```

二进制数和二进制点的位置都在该值内编码。

`decimal`是一个浮点小数点类型。换句话说，它们代表了这样的数字：

```
12345.65789
```

同样，小数点的数量和位置都在值内编码 - `decimal`仍然是浮点类型而不是固定点类型。

float和double还是二进制的value来表示十进制的数字
但是,decimal本身就是十进制的value,(注意,decimal仍然是个浮点类型)

需要注意的重要一点是，人类习惯于以十进制形式表示非整数，并期望十进制表示中的精确结果; 并非所有十进制数都可以在二进制浮点中精确表示 - 例如0.1 - 所以如果使用二进制浮点值，实际上会得到0.1的近似值。在使用浮动小数点时，您仍然可以获得近似值 - 例如，将1除以3的结果无法准确表示。

**2、decimal、float 、double的使用**



- 对于“自然精确小数”的值，使用decimal就很好。这通常适用于人类发明的任何概念：财务价值是最明显的例子，但也有其他概念。例如，考虑给予潜水员或滑冰者的分数。
- 对于更多无法被精确测量值，float/double更合适。例如，科学数据通常以这种形式表示。在这里，原始值不会以“十进制精度”开头，因此保持“十进制精度”对于预期结果并不重要。浮点二进制类型比小数点快得多。



**3、精度区别**



```
/==========================================================================================
    Type       Bits    Have up to                   Approximate Range 
/==========================================================================================
    float      32      7 digits                     -3.4 × 10 ^ (38)   to +3.4 × 10 ^ (38)
    double     64      15-16 digits                 ±5.0 × 10 ^ (-324) to ±1.7 × 10 ^ (308)
    decimal    128     28-29 significant digits     ±7.9 x 10 ^ (28) or (1 to 10 ^ (28)
/==========================================================================================
+---------+----------------+---------+----------+---------------------------------------------+
| C#      | .Net Framework | Signed? | Bytes    | Possible Values                             |
| Type    | (System) type  |         | Occupied |                                             |
+---------+----------------+---------+----------+---------------------------------------------+
| sbyte   | System.Sbyte   | Yes     | 1        | -128 to 127                                 |
| short   | System.Int16   | Yes     | 2        | -32768 to 32767                             |
| int     | System.Int32   | Yes     | 4        | -2147483648 to 2147483647                   |
| long    | System.Int64   | Yes     | 8        | -9223372036854775808 to 9223372036854775807 |
| byte    | System.Byte    | No      | 1        | 0 to 255                                    |
| ushort  | System.Uint16  | No      | 2        | 0 to 65535                                  |
| uint    | System.UInt32  | No      | 4        | 0 to 4294967295                             |
| ulong   | System.Uint64  | No      | 8        | 0 to 18446744073709551615                   |
| float   | System.Single  | Yes     | 4        | Approximately ±1.5 x 10-45 to ±3.4 x 1038   |
|         |                |         |          |  with 7 significant figures                 |
| double  | System.Double  | Yes     | 8        | Approximately ±5.0 x 10-324 to ±1.7 x 10308 |
|         |                |         |          |  with 15 or 16 significant figures          |
| decimal | System.Decimal | Yes     | 12       | Approximately ±1.0 x 10-28 to ±7.9 x 1028   |
|         |                |         |          |  with 28 or 29 significant figures          |
| char    | System.Char    | N/A     | 2        | Any Unicode character (16 bit)              |
| bool    | System.Boolean | N/A     | 1 / 2    | true or false                               |
+---------+----------------+---------+----------+---------------------------------------------+
```

**相关文档**：http://social.msdn.microsoft.com/Forums/en-US/csharpgeneral/thread/921a8ffc-9829-4145-bdc9-a96c1ec174a5