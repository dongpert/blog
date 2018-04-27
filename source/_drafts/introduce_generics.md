---
title: 'generics '
tags:
---
Generic classes and methods combine reusability, type safety and efficiency in a way that their non-generic counterparts cannot. Generics are most frequently used with collections and the methods that operate on them. Version 2.0 of the .NET Framework class library provides a new namespace, System.Collections.Generic, which contains several new generic-based collection classes. It is recommended that all applications that target the .NET Framework 2.0 and later use the new generic collection classes instead of the older non-generic counterparts such as ArrayList. For more information, see Generics in the .NET Framework Class Library.

Of course, you can also create custom generic types and methods to provide your own generalized solutions and design patterns that are type-safe and efficient. The following code example shows a simple generic linked-list class for demonstration purposes. (In most cases, you should use the List<T> class provided by the .NET Framework class library instead of creating your own.) The type parameter T is used in several locations where a concrete type would ordinarily be used to indicate the type of the item stored in the list. It is used in the following ways:

As the type of a method parameter in the AddHead method.

As the return type of the Data property in the nested Node class.

As the type of the private member data in the nested class.

Note that T is available to the nested Node class. When GenericList<T> is instantiated with a concrete type, for example as a GenericList<int>, each occurrence of T will be replaced with int.

~~~
// type parameter T in angle brackets
public class GenericList<T> 
{
    // The nested class is also generic on T.
    private class Node
    {
        // T used in non-generic constructor.
        public Node(T t)
        {
            next = null;
            data = t;
        }

        private Node next;
        public Node Next
        {
            get { return next; }
            set { next = value; }
        }
        
        // T as private member data type.
        private T data;

        // T as return type of property.
        public T Data  
        {
            get { return data; }
            set { data = value; }
        }
    }

    private Node head;
    
    // constructor
    public GenericList() 
    {
        head = null;
    }

    // T as method parameter type:
    public void AddHead(T t) 
    {
        Node n = new Node(t);
        n.Next = head;
        head = n;
    }

    public IEnumerator<T> GetEnumerator()
    {
        Node current = head;

        while (current != null)
        {
            yield return current.Data;
            current = current.Next;
        }
    }
}
~~~

The following code example shows how client code uses the generic GenericList<T> class to create a list of integers. Simply by changing the type argument, the following code could easily be modified to create lists of strings or any other custom type:

~~~
class TestGenericList
{
    static void Main()
    {
        // int is the type argument
        GenericList<int> list = new GenericList<int>();

        for (int x = 0; x < 10; x++)
        {
            list.AddHead(x);
        }

        foreach (int i in list)
        {
            System.Console.Write(i + " ");
        }
        System.Console.WriteLine("\nDone");
    }
}
~~~