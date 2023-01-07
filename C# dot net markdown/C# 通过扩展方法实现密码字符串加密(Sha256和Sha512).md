##  .NET Core 通过扩展方法实现密码字符串加密(Sha256和Sha512)


本文主要介绍在.NET Core中通过扩展方法，实现Sha256和Sha512方法进行字符串加密。可以直接用字符串对象调用方法进行加密。

**1、字符串扩展方法类加密方法实现**

```
// Copyright (c) Brock Allen & Dominick Baier. All rights reserved.
// Licensed under the Apache License, Version 2.0. See LICENSE in the project root for license information.
using System;
using System.Security.Cryptography;
using System.Text;
namespace SPACore.Extensions
{
    /// <summary>
    /// Extension methods for hashing strings
    /// </summary>
    public static class HashExtensions
    {
        /// <summary>
        /// Creates a SHA256 hash of the specified input.
        /// </summary>
        /// <param name="input">The input.</param>
        /// <returns>A hash</returns>
        public static string Sha256(this string input)
        {
            if (String.IsNullOrEmpty(input)) return string.Empty;
            using (var sha = SHA256.Create())
            {
                var bytes = Encoding.UTF8.GetBytes(input);
                var hash = sha.ComputeHash(bytes);
                return Convert.ToBase64String(hash);
            }
        }
        /// <summary>
        /// Creates a SHA256 hash of the specified input.
        /// </summary>
        /// <param name="input">The input.</param>
        /// <returns>A hash.</returns>
        public static byte[] Sha256(this byte[] input)
        {
            if (input == null)
            {
                return null;
            }
            using (var sha = SHA256.Create())
            {
                return sha.ComputeHash(input);
            }
        }
        /// <summary>
        /// Creates a SHA512 hash of the specified input.
        /// </summary>
        /// <param name="input">The input.</param>
        /// <returns>A hash</returns>
        public static string Sha512(this string input)
        {
            if (string.IsNullOrEmpty(input)) return string.Empty;
            using (var sha = SHA512.Create())
            {
                var bytes = Encoding.UTF8.GetBytes(input);
                var hash = sha.ComputeHash(bytes);
                return Convert.ToBase64String(hash);
            }
        }
    }
}
```





**2、Sha256和Sha512扩展方法调用**

```
using SPACore.Data;
using SPACore.Utils;
using System;
using System.Threading.Tasks;
using SPACore.Extensions;//需要添加扩展方法命名空间
namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
            string Pwd = "Aa123456";
            //直接通过字符串扩展方法调用
            Console.WriteLine(Pwd.Sha256());
            Console.WriteLine(Pwd.Sha512());
            Console.ReadKey();
        }
    }
} 
```





**注意**：

1. 使用扩展的加密方法，需要添加扩展方法实现类`HashExtensions`的命令空间
2. 直接通过字符串点方法(`Pwd.Sha256()`)调用