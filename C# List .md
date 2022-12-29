## List

https://www.geeksforgeeks.org/list-implementation-in-c-sharp/?ref=lbp

```c#
// Creating List using List class 
// and Adding elements using the 
// Collection initializers 
List<string> my_list1 = new List<string>() { “geeks”, “Geek123”, “GeeksforGeeks” }; 
```
## foreach
```c#
// Accessing elements of my_list 
// Using foreach loop 
foreach(int a in my_list) 
{ 
Console.WriteLine(a); 
} 
```

## ForEach()

```c#
// Accessing elements of my_list 
// Using ForEach method 
my_list.ForEach(a = > Console.WriteLine(a)); 
```

## **Using for loop**

```c#
// Accessing elements of my_list 
// Using for loop 
for (int a = 0; a < my_list.Count; a++) 
{ 
Console.WriteLine(my_list[a]); 
} 
```



## **Using indexers**

```c#
// Accessing elements of my_list 
// Using indexers 
Console.WriteLine(my_list[3]); 
Console.WriteLine(my_list[4]); 
```

#### How to remove elements from the List?

You can remove the elements of the list by using the following methods provided by List<T> class:

- [**Remove(T)**](https://www.geeksforgeeks.org/c-removing-the-specified-element-from-the-list/)**:** This method is used to remove the first occurrence of a specific object from the List.
- [**RemoveAll(Predicate)**](https://www.geeksforgeeks.org/c-remove-all-elements-of-a-list-that-match-the-conditions-defined-by-the-predicate/)**:** This method is used to remove all the elements that match the conditions defined by the specified predicate.
- [**RemoveAt(Int32)**](https://www.geeksforgeeks.org/c-how-to-remove-the-element-from-the-specified-index-of-the-list/)**:** This method is used to remove the element at the specified index of the List.
- [**RemoveRange(Int32, Int32)**](https://www.geeksforgeeks.org/c-removing-a-range-of-elements-from-the-list/)**:** This method is used to remove a range of elements from the List<T>.
- [**Clear()**](https://www.geeksforgeeks.org/c-removing-all-the-elements-from-the-list/)**:** This method is used to remove all elements from the List<T>.

```c#
// C# program to illustrate how
// to remove objects from the list
using System;
using System.Collections.Generic;

class GFG {

	// Main Method
	static public void Main()
	{

		// Creating list using List class
		// and List<T>() Constructor
		List<int> my_list = new List<int>();

		// Adding elements to List
		// Using Add() method
		my_list.Add(1);
		my_list.Add(10);
		my_list.Add(100);
		my_list.Add(1000);
		my_list.Add(10000);
		my_list.Add(100000);
		my_list.Add(1000000);
		my_list.Add(10000000);
		my_list.Add(100000000);

		// Initial count
		Console.WriteLine("Initial count:{0}", my_list.Count);

		// After using Remove() method
		my_list.Remove(10);
		Console.WriteLine("2nd count:{0}", my_list.Count);

		// After using RemoveAt() method
		my_list.RemoveAt(4);
		Console.WriteLine("3rd count:{0}", my_list.Count);

		// After using RemoveRange() method
		my_list.RemoveRange(0, 2);
		Console.WriteLine("4th count:{0}", my_list.Count);

		// After using Clear() method
		my_list.Clear();
		Console.WriteLine("5th count:{0}", my_list.Count);
	}
}

```

**Output:** 

```
Initial count:9
2nd count:8
3rd count:7
4th count:5
5th count:0
```

#### How to sort list?

You can sort the elements by using Sort() method. This method is used to sort the elements or a portion of the elements in the List<T> using either the specified or default IComparer<T> implementation or a provided Comparison<T> delegate to compare list elements.

**Example:** 

```c#
// C# program to illustrate how
// sort a list
using System;
using System.Collections.Generic;

class GFG {

	// Main Method
	static public void Main()
	{

		// Creating list using List class
		// and List<T>() Constructor
		List<int> my_list = new List<int>();

		// Adding elements to List
		// Using Add() method
		my_list.Add(496);
		my_list.Add(1000);
		my_list.Add(100);
		my_list.Add(10);
		my_list.Add(10000);
		my_list.Add(10000000);
		my_list.Add(1000000);
		my_list.Add(100000);
		my_list.Add(0000);

		// Without sorted List
		Console.WriteLine("UnSorted List:");
		foreach(int a in my_list)
		{
			Console.WriteLine(a);
		}

		// After Sort method
		my_list.Sort();
		Console.WriteLine("Sorted List:");
		foreach(int a in my_list)
		{
			Console.WriteLine(a);
		}
	}
}

```

**Output:** 

```
UnSorted List:
496
1000
100
10
10000
10000000
1000000
100000
0
Sorted List:
0
10
100
496
1000
10000
100000
1000000
10000000
```