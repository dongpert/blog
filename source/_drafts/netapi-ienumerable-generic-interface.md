---
title: netapi-ienumerable--generic-interface
tags:
---
## Examples
The following example demonstrates how to implement the IEnumerable<T> interface and how to use that implementation to create a LINQ query. When you implement IEnumerable<T>, you must also implement IEnumerator<T> or, for C# only, you can use the yield keyword. Implementing IEnumerator<T> also requires IDisposable to be implemented, which you will see in this example.

이 에제는 IEnumerable<T> 인터페이스를 구현하는 방법과 LINQ 쿼리를 생성하기 위해 구현을 사용하는 방법을 보여줍니다. IEumerable<T>를 구현하면 IEnumerator<T>을 구현해야하며 C#의 경우 yield 키워드를 사용할 수 있습니다. IEnumerator<T>는 또한 구현된 IDisposabl가 필요합니다.

~~~
using System;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Linq;

public class App
{
    // Excercise the Iterator and show that it's more
    // performant.
    public static void Main()
    {
        TestStreamReaderEnumerable();
        Console.WriteLine("---");
        TestReadingFile();
    }

    public static void TestStreamReaderEnumerable()
	{
		// Check the memory before the iterator is used.
		long memoryBefore = GC.GetTotalMemory(true);
      IEnumerable<String> stringsFound;
		// Open a file with the StreamReaderEnumerable and check for a string.
      try {
         stringsFound =
               from line in new StreamReaderEnumerable(@"c:\temp\tempFile.txt")
               where line.Contains("string to search for")
               select line;
         Console.WriteLine("Found: " + stringsFound.Count());
      }
      catch (FileNotFoundException) {
         Console.WriteLine(@"This example requires a file named C:\temp\tempFile.txt.");
         return;
      }

		// Check the memory after the iterator and output it to the console.
		long memoryAfter = GC.GetTotalMemory(false);
		Console.WriteLine("Memory Used With Iterator = \t"
            + string.Format(((memoryAfter - memoryBefore) / 1000).ToString(), "n") + "kb");
	}

    public static void TestReadingFile()
	{
		long memoryBefore = GC.GetTotalMemory(true);
      StreamReader sr;
      try {
         sr = File.OpenText("c:\\temp\\tempFile.txt");
      }
      catch (FileNotFoundException) {
         Console.WriteLine(@"This example requires a file named C:\temp\tempFile.txt.");
         return;
      }

        // Add the file contents to a generic list of strings.
		List<string> fileContents = new List<string>();
		while (!sr.EndOfStream) {
			fileContents.Add(sr.ReadLine());
		}

		// Check for the string.
		var stringsFound = 
            from line in fileContents
            where line.Contains("string to search for")
            select line;

        sr.Close();
        Console.WriteLine("Found: " + stringsFound.Count());

		// Check the memory after when the iterator is not used, and output it to the console.
		long memoryAfter = GC.GetTotalMemory(false);
		Console.WriteLine("Memory Used Without Iterator = \t" + 
            string.Format(((memoryAfter - memoryBefore) / 1000).ToString(), "n") + "kb");
	}
}

// A custom class that implements IEnumerable(T). When you implement IEnumerable(T), 
// you must also implement IEnumerable and IEnumerator(T).
public class StreamReaderEnumerable : IEnumerable<string>
{
    private string _filePath;
    public StreamReaderEnumerable(string filePath)
    {
        _filePath = filePath;
    }

    // Must implement GetEnumerator, which returns a new StreamReaderEnumerator.
    public IEnumerator<string> GetEnumerator()
    {
        return new StreamReaderEnumerator(_filePath);
    }

    // Must also implement IEnumerable.GetEnumerator, but implement as a private method.
    private IEnumerator GetEnumerator1()
    {
        return this.GetEnumerator();
    }
    IEnumerator IEnumerable.GetEnumerator()
    {
        return GetEnumerator1();
    }
}

// When you implement IEnumerable(T), you must also implement IEnumerator(T), 
// which will walk through the contents of the file one line at a time.
// Implementing IEnumerator(T) requires that you implement IEnumerator and IDisposable.
public class StreamReaderEnumerator : IEnumerator<string>
{
    private StreamReader _sr;
    public StreamReaderEnumerator(string filePath)
    {
        _sr = new StreamReader(filePath);
    }

    private string _current;
    // Implement the IEnumerator(T).Current publicly, but implement 
    // IEnumerator.Current, which is also required, privately.
    public string Current
    {

        get
        {
            if (_sr == null || _current == null)
            {
                throw new InvalidOperationException();
            }

            return _current;
        }
    }

    private object Current1
    {

        get { return this.Current; }
    }

    object IEnumerator.Current
    {
        get { return Current1; }
    }

    // Implement MoveNext and Reset, which are required by IEnumerator.
    public bool MoveNext()
    {
        _current = _sr.ReadLine();
        if (_current == null)
            return false;
        return true;
    }

    public void Reset()
    {
        _sr.DiscardBufferedData();
        _sr.BaseStream.Seek(0, SeekOrigin.Begin);
        _current = null;
    }

    // Implement IDisposable, which is also implemented by IEnumerator(T).
    private bool disposedValue = false;
    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this);
    }

    protected virtual void Dispose(bool disposing)
    {
        if (!this.disposedValue)
        {
            if (disposing)
            {
                // Dispose of managed resources.
            }
            _current = null;
            if (_sr != null) {
               _sr.Close();
               _sr.Dispose();
            }
        }

        this.disposedValue = true;
    }

     ~StreamReaderEnumerator()
    {
        Dispose(false);
    }
}
// This example displays output similar to the following:
//       Found: 2
//       Memory Used With Iterator =     33kb
//       ---
//       Found: 2
//       Memory Used Without Iterator =  206kb
~~~
For another C# example that demonstrates how to implement the IEnumerable<T> interface, see the Generics Sample. This sample uses of the yield keyword instead of implementing IEnumerator<T>.

## Remarks
The returned IEnumerator\<T\> provides the ability to iterate through the collection by exposing a Current property .You can use enumerators to read the data in a collection, but not to modify the collection.

반환된 IEnumerator\<T\>는 Current 속성을 노출하여 컬렉션을 반복할 있는 기능을 제공합니다. 컬렉션에 데어타를 읽기 위해 enumerators를 사용할 수 있지만 컬렉션을 수정할 수 없습니다.

Initially, the enumerator is positioned before the first element in the collection. At this position, Current is undefined. Therefore, you must call the MoveNext method to advance the enumerator to the first element of the collection before reading the value of Current.

Current returns the same object until MoveNext is called again as MoveNext sets Current to the next element.

If MoveNext passes the end of the collection, the enumerator is positioned after the last element in the collection and MoveNext returns false. When the enumerator is at this position, subsequent calls to MoveNext also return false. If the last call to MoveNext returned false, Current is undefined. You cannot set Current to the first element of the collection again; you must create a new enumerator instance instead.

If changes are made to the collection, such as adding, modifying, or deleting elements, the behavior of the enumerator is undefined.

An enumerator does not have exclusive access to the collection so an enumerator remains valid as long as the collection remains unchanged. If changes are made to the collection, such as adding, modifying, or deleting elements, the enumerator is invalidated and you may get unexpected results. Also, enumerating a collection is not a thread-safe procedure. To guarantee thread-safety, you should lock the collection during enumerator or implement synchronization on the collection.

enumerator는 컬렉션에 베타적 접근을 할 수 없으므로 enumerator는 컬렉션이 변경되지 않는 한 유효합니다. 컬렉션이 변경이 되면 enumerator는 무효화되고 예상치 않은 결과를 얻을 것입니다. 

Default implementations of collections in the System.Collections.Generic namespace aren't synchronized.