# C# Hashtable with Examples

A Hashtable is a collection of key/value pairs that are arranged based on the hash code of the key. Or in other words, a Hashtable is used to create a collection which uses a hash table for storage. It generally optimized the lookup by calculating the hash code of every key and store into another basket automatically and when you accessing the value from the hashtable at that time it matches the hashcode with the specified key. It is the non-generic type of collection which is defined in *System.Collections* namespace. 

**Important Points:** 

- In Hashtable, the key cannot be null, but value can be.
- In Hashtable, key objects must be immutable as long as they are used as keys in the Hashtable.
- The capacity of a Hashtable is the number of elements that Hashtable can hold.
- A hash function is provided by each key object in the Hashtable.
- The Hashtable class implements the *IDictionary*, *ICollection*, *IEnumerable*, *ISerializable*, *IDeserializationCallback*, and *ICloneable* interfaces.
- In hashtable, you can store elements of the same type and of the different types.
- The elements of hashtable that is a key/value pair are stored in DictionaryEntry, so you can also cast the key/value pairs to a *DictionaryEntry*.
- In Hashtable, key must be unique. Duplicate keys are not allowed.

#### How to create a Hashtable?

Hashtable class provides **16** different types of constructors which are used to create a hashtable, here we only use *Hashtable() constructor*. To read more about Hashtable’s constructors you can refer to [**C# | Hashtable Class**](https://www.geeksforgeeks.org/c-sharp-hashtable-class/) This constructor is used to create an instance of the Hashtable class which is empty and having the default initial capacity, load factor, hash code provider, and comparer. Now, let’s see how to create a hashtable using Hashtable() constructor:

**Step 1:** Include System.Collections namespace in your program with the help of *using* keyword: 

```
using System.Collections;
```

**Step 2:** Create a hashtable using Hashtable class as shown below: 

```
Hashtable hashtable_name = new Hashtable();
```

**Step 3:** If you want to add a key/value pair in your hashtable, then use Add() method to add elements in your hashtable. And you can also store a key/value pair in your hashtable without using [Add() method](https://www.geeksforgeeks.org/c-adding-an-element-into-the-hashtable/). 

**Example:** 

```c#
// C# program to illustrate how
// to create a hashtable
using System;
using System.Collections;

class GFG {

	// Main Method
	static public void Main()
	{

		// Create a hashtable
		// Using Hashtable class
		Hashtable my_hashtable1 = new Hashtable();

		// Adding key/value pair
		// in the hashtable
		// Using Add() method
		my_hashtable1.Add("A1", "Welcome");
		my_hashtable1.Add("A2", "to");
		my_hashtable1.Add("A3", "GeeksforGeeks");

		Console.WriteLine("Key and Value pairs from my_hashtable1:");

		foreach(DictionaryEntry ele1 in my_hashtable1)
		{
			Console.WriteLine("{0} and {1} ", ele1.Key, ele1.Value);
		}

		// Create another hashtable
		// Using Hashtable class
		// and adding key/value pairs
		// without using Add method
		Hashtable my_hashtable2 = new Hashtable() {
									{1, "hello"},
										{2, 234},
										{3, 230.45},
										{4, null}};

		Console.WriteLine("Key and Value pairs from my_hashtable2:");

		foreach(var ele2 in my_hashtable2.Keys)
		{
			Console.WriteLine("{0}and {1}", ele2,
							my_hashtable2[ele2]);
		}
	}
}

```

**Output:** 

```
Key and Value pairs from my_hashtable1:
A3 and GeeksforGeeks 
A2 and to 
A1 and Welcome 
Key and Value pairs from my_hashtable2:
4and 
3and 230.45
2and 234
1and hello
```

#### How to remove elements from the hashtable?

In Hashtable, you are allowed to remove elements from the hashtable. The Hashtable class provides two different methods to remove elements and the methods are:

- [**Clear** ](https://www.geeksforgeeks.org/c-remove-all-elements-from-the-hashtable/)**:** This method is used to remove all the objects from the hashtable.
- [**Remove** ](https://www.geeksforgeeks.org/c-remove-the-element-with-the-specified-key-from-the-hashtable/)**:** This method is used to remove the element with the specified key from the hashtable.

**Example:**

```c#
// C# program to illustrate how
// remove elements from the hashtable
using System;
using System.Collections;

class GFG {

	// Main Method
	static public void Main()
	{

		// Create a hashtable
		// Using Hashtable class
		Hashtable my_hashtable = new Hashtable();

		// Adding key/value pair
		// in the hashtable
		// Using Add() method
		my_hashtable.Add("A1", "Welcome");
		my_hashtable.Add("A2", "to");
		my_hashtable.Add("A3", "GeeksforGeeks");

		// Using remove method
		// remove A2 key/value pair
		my_hashtable.Remove("A2");

		Console.WriteLine("Key and Value pairs :");

		foreach(DictionaryEntry ele1 in my_hashtable)
		{
			Console.WriteLine("{0} and {1} ", ele1.Key, ele1.Value);
		}

		// Before using Clear method
		Console.WriteLine("Total number of elements present"+
				" in my_hashtable:{0}", my_hashtable.Count);

		my_hashtable.Clear();

		// After using Clear method
		Console.WriteLine("Total number of elements present in"+
					" my_hashtable:{0}", my_hashtable.Count);
	}
}

```

**Output:** 

```
Key and Value pairs :
A3 and GeeksforGeeks 
A1 and Welcome 
Total number of elements present in my_hashtable:2
Total number of elements present in my_hashtable:0
```

#### How to check the availability of key/value pair in hashtable?

In hashtable, you can check whether the given pair is present or not using the following methods:

- [**Contains**](https://www.geeksforgeeks.org/c-check-whether-a-hashtable-contains-a-specific-key-or-not/)**:** This method is used to check whether the Hashtable contains a specific key.
- [**ContainsKey**](https://www.geeksforgeeks.org/c-check-if-the-hashtable-contains-a-specific-key/)**:** This method is also used to check whether the Hashtable contains a specific key.
- [**ContainsValue**](https://www.geeksforgeeks.org/c-check-if-the-hashtable-contains-a-specific-value/)**:** This method is used to check whether the Hashtable contains a specific value.

**Example:** 

```c#
// C# program to illustrate how
// to check key/value present
// in the hashtable or not
using System;
using System.Collections;

class GFG {

	// Main Method
	static public void Main()
	{

		// Create a hashtable
		// Using Hashtable class
		Hashtable my_hashtable = new Hashtable();

		// Adding key/value pair in the hashtable
		// Using Add() method
		my_hashtable.Add("A1", "Welcome");
		my_hashtable.Add("A2", "to");
		my_hashtable.Add("A3", "GeeksforGeeks");

		// Determine whether the given
		// key present or not
		// using Contains method
		Console.WriteLine(my_hashtable.Contains("A3"));
		Console.WriteLine(my_hashtable.Contains(12));
		Console.WriteLine();

		// Determine whether the given
		// key present or not
		// using ContainsKey method
		Console.WriteLine(my_hashtable.ContainsKey("A1"));
		Console.WriteLine(my_hashtable.ContainsKey(1));
		Console.WriteLine();

		// Determine whether the given
		// value present or not
		// using ContainsValue method
		Console.WriteLine(my_hashtable.ContainsValue("geeks"));
		Console.WriteLine(my_hashtable.ContainsValue("to"));
	}
}

```

**Output:** 

```
True
False

True
False

False
True
```