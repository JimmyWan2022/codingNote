# Linked List Implementation in C#

A [**LinkedList** ](https://www.geeksforgeeks.org/linked-list-set-1-introduction/)is a linear data structure which stores element in the non-contiguous location. The elements in a linked list are linked with each other using pointers. Or in other words, LinkedList consists of nodes where each node contains a data field and a reference(link) to the next node in the list. In C#, LinkedList is the generic type of collection which is defined in **System.Collections.Generic** namespace. It is a *doubly linked list*, therefore, each node points forward to the Next node and backward to the Previous node. It is a dynamic collection which grows, according to the need of your program. It also provides fast inserting and removing elements. ![img](https://media.geeksforgeeks.org/wp-content/uploads/20190326091540/Untitled-Diagram11.jpg) **Important Points:**

- The LinkedList class implements the *ICollection<T>*, *IEnumerable<T>*, *IReadOnlyCollection<T>*, *ICollection*, *IEnumerable*, *IDeserializationCallback*, and *ISerializable* interfaces.
- It also supports enumerators.
- You can remove nodes and reinsert them, either in the same list or in another list, which results in no additional objects allocated on the heap.
- Every node in a LinkedList<T> object is of the type LinkedListNode<T>.
- It does not support chaining, splitting, cycles, or other features that can leave the list in an inconsistent state.
- If the LinkedList is empty, the First and Last properties contain null.
- The capacity of a LinkedList is the number of elements the LinkedList can hold.
- In LinkedList, it is allowed to store duplicate elements but of the same type.

#### How to create a LinkedList?

A LinkedList class has 3 constructors which are used to create a LinkedList which are as follows:

- **LinkedList():** This constructor is used to create an instance of the LinkedList class that is empty.
- **LinkedList(IEnumerable):** This constructor is used to create an instance of the LinkedList class that contains elements copied from the specified IEnumerable and has sufficient capacity to accommodate the number of elements copied.
- **LinkedList(SerializationInfo, StreamingContext):** This constructor is used to create an instance of the LinkedList class that is serializable with the specified SerializationInfo and StreamingContext.

Let’s see how to create an LinkedList using *LinkedList()* constructor: **Step 1:** Include *System.Collections.Generic* namespace in your program with the help of *using* keyword:

```
using System.Collections.Generic;
```

**Step 2:** Create a LinkedList using LinkedList class as shown below:

> LinkedList <Type_of_linkedlist> linkedlist_name = new LinkedList <Type_of_linkedlist>();

**Step 3:** LinkedList provides 4 different methods to add nodes and these methods are:

- **AddAfter:** This method is used to add a new node or value after an existing node in the LinkedList.
- **AddBefore:** This method is used to add a new node or value before an existing node in the LinkedList.
- [**AddFirst**](https://www.geeksforgeeks.org/c-adding-new-node-or-value-at-the-start-of-linkedlistt/)**:** This method is used to add a new node or value at the start of the LinkedList.
- [**AddLast**](https://www.geeksforgeeks.org/c-adding-new-node-or-value-at-the-end-of-linkedlistt/)**:** This method is used to add a new node or value at the end of the LinkedList.

**Step 4:** The elements of the LinkedList is accessed by using a foreach loop or by using for loop. As shown in the below example. **Example:** 

## CSharp

```c#
// C# program to illustrate how 
// to create a LinkedList
using System;
using System.Collections.Generic;
  
class GFG {
  
    // Main Method
    static public void Main()
    {
  
        // Creating a linkedlist
        // Using LinkedList class
        LinkedList<String> my_list = new LinkedList<String>();
  
        // Adding elements in the LinkedList
        // Using AddLast() method
        my_list.AddLast("Zoya");
        my_list.AddLast("Shilpa");
        my_list.AddLast("Rohit");
        my_list.AddLast("Rohan");
        my_list.AddLast("Juhi");
        my_list.AddLast("Zoya");
  
        Console.WriteLine("Best students of XYZ university:");
  
        // Accessing the elements of 
        // LinkedList Using foreach loop
        foreach(string str in my_list)
        {
            Console.WriteLine(str);
        }
    }
}
```

**Output:**

```
Best students of XYZ university:
Zoya
Shilpa
Rohit
Rohan
Juhi
Zoya
```

#### How to remove elements from the LinkedList?

In LinkedList, it is allowed to remove elements from the LinkedList. LinkedList<T> class provides 5 different methods to remove elements and the methods are:

- [**Clear()**](https://www.geeksforgeeks.org/c-removing-all-nodes-from-linkedlistt/)**:** This method is used to remove all nodes from the LinkedList.
- [**Remove(LinkedListNode)**](https://www.geeksforgeeks.org/c-removing-the-specified-node-from-the-linkedlistt/)**:** This method is used to remove the specified node from the LinkedList.
- [**Remove(T)**](https://www.geeksforgeeks.org/c-removing-first-occurrence-of-specified-value-from-linkedlistt/)**:** This method is used to remove the first occurrence of the specified value from the LinkedList.
- [**RemoveFirst()**](https://www.geeksforgeeks.org/c-removing-the-node-at-the-start-of-the-linkedlistt/)**:** This method is used to remove the node at the start of the LinkedList.
- [**RemoveLast()**](https://www.geeksforgeeks.org/c-removing-the-node-at-the-start-of-the-linkedlistt/)**:** This method is used to remove the node at the end of the LinkedList.

**Example:** 

## CSharp

```c#
// C# program to illustrate how to
// remove elements from LinkedList
using System;
using System.Collections.Generic;
  
class GFG {
  
    // Main Method
    static public void Main()
    {
  
        // Creating a linkedlist
        // Using LinkedList class
        LinkedList<String> my_list = new LinkedList<String>();
  
        // Adding elements in the LinkedList
        // Using AddLast() method
        my_list.AddLast("Zoya");
        my_list.AddLast("Shilpa");
        my_list.AddLast("Rohit");
        my_list.AddLast("Rohan");
        my_list.AddLast("Juhi");
        my_list.AddLast("Zoya");
  
        // Initial number of elements
        Console.WriteLine("Best students of XYZ "+
                         "university initially:");
  
        // Accessing the elements of 
        // Linkedlist Using foreach loop
        foreach(string str in my_list)
        {
            Console.WriteLine(str);
        }
  
        // After using Remove(LinkedListNode)
        // method
        Console.WriteLine("Best students of XYZ"+
                         " university in 2000:");
  
        my_list.Remove(my_list.First);
  
        foreach(string str in my_list)
        {
            Console.WriteLine(str);
        }
  
        // After using Remove(T) method
        Console.WriteLine("Best students of XYZ"+
                         " university in 2001:");
  
        my_list.Remove("Rohit");
  
        foreach(string str in my_list)
        {
            Console.WriteLine(str);
        }
  
        // After using RemoveFirst() method
        Console.WriteLine("Best students of XYZ"+
                         " university in 2002:");
  
        my_list.RemoveFirst();
  
        foreach(string str in my_list)
        {
            Console.WriteLine(str);
        }
  
        // After using RemoveLast() method
        Console.WriteLine("Best students of XYZ"+
                         " university in 2003:");
  
        my_list.RemoveLast();
  
        foreach(string str in my_list)
        {
            Console.WriteLine(str);
        }
  
        // After using Clear() method
        my_list.Clear();
        Console.WriteLine("Number of students: {0}",
                                     my_list.Count);
    }
}
```

**Output:**

```
Best students of XYZ university initially:
Zoya
Shilpa
Rohit
Rohan
Juhi
Zoya
Best students of XYZ university in 2000:
Shilpa
Rohit
Rohan
Juhi
Zoya
Best students of XYZ university in 2001:
Shilpa
Rohan
Juhi
Zoya
Best students of XYZ university in 2002:
Rohan
Juhi
Zoya
Best students of XYZ university in 2003:
Rohan
Juhi
Number of students: 0
```

#### How to check the availability of the elements in the LinkedList?

In LinkedList, you can check whether the given value is present or not using the [Contains(T)](https://www.geeksforgeeks.org/c-check-if-a-value-is-in-linkedlistt/) method. This method is used to determine whether a value is in the LinkedList. **Example:** 

## CSharp

```c#
// C# program to illustrate how 
// to check whether the given 
// element is present or not 
// in the LinkedList
using System;
using System.Collections.Generic;
  
class GFG {
  
    // Main Method
    static public void Main()
    {
  
        // Creating a linkedlist
        // Using LinkedList class
        LinkedList<String> my_list = new LinkedList<String>();
  
        // Adding elements in the Linkedlist
        // Using AddLast() method
        my_list.AddLast("Zoya");
        my_list.AddLast("Shilpa");
        my_list.AddLast("Rohit");
        my_list.AddLast("Rohan");
        my_list.AddLast("Juhi");
  
        // Check if the given element
        // is available or not
        if (my_list.Contains("Shilpa") == true) 
        {
            Console.WriteLine("Element Found...!!");
        }
        else 
        {
            Console.WriteLine("Element Not found...!!");
        }
    }
}
```

**Output:**

```
Element Found...!!
```