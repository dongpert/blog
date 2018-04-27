---
title: using_objects_that_implement_idisposable
tags:
---
The common language runtime's garbage collector reclaims the memory used by managed objects, but types that use unmanaged resources implement the IDisposable interface to allow the memory allocated to these unmanaged resources to be reclaimed. When you finish using an object that implements IDisposable, you should call the object's IDisposable.Dispose implementation. You can do this in one of two ways:

공용 언어 런타임의 가비지 수집기는 관리되는 객체가 사용하는 메모리를 회수합니다. 그러나 비관리 리소스 형식은 비관리 리소스에 할당되 있는 메모리를 회수할 수 있게 하는 IDisposable 인터페이스를 구현합니다. IDisposable을 구현하는 개체 사용이 끝나면 객체의 IDisposable.Dispose 구현을 호출해야 합니다.

 * With the C# using statement or the Visual Basic Using statement.
 * C# using문 또는 Visual Basic Using문 사용

 * By implementing a try/finally block.
 * try/finally 블럭 구현

## The using statement
The using statement in C# and the Using statement in Visual Basic simplify the code that you must write to create and clean up an object. The using statement obtains one or more resources, executes the statements that you specify, and automatically disposes of the object. However, the using statement is useful only for objects that are used within the scope of the method in which they are constructed.

C#의 using문과 비주얼베이직의 Using문은 객체를 생성하고 삭제하기 위해 작성해야 하는 코드를 간소화합니다. using문은 하나이상의 객체를 얻어 지정한 구문을 실행하고 자동으로 객체를 처리합니다. 그러나 using문은 그들이 생성된 메서드 범위 내에서 사용되는 개체에만 유용합니다.

The following example uses the using statement to create and release a System.IO.StreamReader object.

~~~
using System;
using System.IO;

public class Example
{
   public static void Main()
   {
      Char[] buffer = new Char[50];
      using (StreamReader s = new StreamReader("File1.txt")) {
         int charsRead = 0;
         while (s.Peek() != -1) {
            charsRead = s.Read(buffer, 0, buffer.Length);
            //
            // Process characters read.
            //   
         }
         s.Close();    
      }

   }
}
~~~

Note that although the StreamReader class implements the IDisposable interface, which indicates that it uses an unmanaged resource, the example doesn't explicitly call the StreamReader.Dispose method. When the C# or Visual Basic compiler encounters the using statement, it emits intermediate language (IL) that is equivalent to the following code that explicitly contains a try/finally block.

StreamReader 클래스가 비관리 리소스를 사용하는 것을 나타내는 IDisposable 인터페이스를 구현하지만 예제에서 분명하게 StreamReader.Dispose 메서드를 호출하지 않았습니다. C# 또는 비주얼 베이직 컴파일러가 using문을 만나면 분명하게 try/finally 블럭을 포함하는 다음 코드와 동일한 중간언어를 내보냅니다.

~~~
using System;
using System.IO;

public class Example
{
   public static void Main()
   {
      Char[] buffer = new Char[50];
      {
         StreamReader s = new StreamReader("File1.txt"); 
         try {
            int charsRead = 0;
            while (s.Peek() != -1) {
               charsRead = s.Read(buffer, 0, buffer.Length);
               //
               // Process characters read.
               //   
            }
            s.Close();
         }
         finally {
            if (s != null)
               ((IDisposable)s).Dispose();     
         }       
      }
   }
}
~~~

The C# using statement also allows you to acquire multiple resources in a single statement, which is internally equivalent to nested using statements. The following example instantiates two StreamReader objects to read the contents of two different files.

C# using문은 중첩 using문과 내부적으로 동등한 단일 구문에서 여러개의 리소스를 얻을 수 있게 허용합니다.

~~~
using System;
using System.IO;

public class Example
{
   public static void Main()
   {
      Char[] buffer1 = new Char[50], buffer2 = new Char[50];
      
      using (StreamReader version1 = new StreamReader("file1.txt"),
                          version2 = new StreamReader("file2.txt")) {
         int charsRead1, charsRead2 = 0;
         while (version1.Peek() != -1 && version2.Peek() != -1) {
            charsRead1 = version1.Read(buffer1, 0, buffer1.Length);
            charsRead2 = version2.Read(buffer2, 0, buffer2.Length);
            //
            // Process characters read.
            //
         }
         version1.Close();
         version2.Close();
      }
   }
}
~~~

## Try/finally block
Instead of wrapping a try/finally block in a using statement, you may choose to implement the try/finally block directly. This may be your personal coding style, or you might want to do this for one of the following reasons:

using문에서 try/finally 블럭을 감싸는 대신에 try/finally 블럭을 직접적으로 구현하는것을 선택할 수 있습니다. 개인 코딩 스타일이거나 다음 이유 중 하나로 인해 이렇게 하기를 원할 수도 있습니다.

* To include a catch block to handle any exceptions thrown in the try block. Otherwise, any exceptions thrown by the using statement are unhandled, as are any exceptions thrown within the using block if a try/catch block isn't present.
* try 블록에서 어느 던저진 예외를 다루기 위한 catch 블록을 포함하기 위해. 다시말해서 using문에 의해 던저진 어느 예외는 다룰 수 없습니다. try/catch 블럭이 없으면 using 블럭내에 던저진 어느 예외처럼

* To instantiate an object that implements IDisposable whose scope is not local to the block within which it is declared.
* 범위가 선언된 블럭의 지역이 아닌 IDisposable를 구현하는 객체를 인스턴스화 하기 위해

The following example is similar to the previous example, except that it uses a try/catch/finally block to instantiate, use, and dispose of a StreamReader object, and to handle any exceptions thrown by the StreamReader constructor and its ReadToEnd method. Note that the code in the finally block checks that the object that implements IDisposable isn't null before it calls the Dispose method. Failure to do this can result in a NullReferenceException exception at run time.

다음 예제는 인스턴스화, 사용, StreamReader 객체처리 그리고 StreamReader 생성자와 ReadToEnd 메서드에 의해 던져진 어떤 예외를 다루기 위해 try/catch/finall 블럭을 사용하는 것을 제외하고 이전예제와 유사합니다. finally 블럭에서 IDisposable를 구현하는 객체를 검사하는 코드는 처리 메서드를 호출하기 전까지는 널이 아닙니다. 이렇게 하지 않으면 런타임에 널참조 예외를 결과를 가져옵니다.

~~~
using System;
using System.Globalization;
using System.IO;

public class Example
{
   public static void Main()
   {
      StreamReader sr = null;
      try {
         sr = new StreamReader("file1.txt");
         String contents = sr.ReadToEnd();
         sr.Close();
         Console.WriteLine("The file has {0} text elements.", 
                           new StringInfo(contents).LengthInTextElements);    
      }      
      catch (FileNotFoundException) {
         Console.WriteLine("The file cannot be found.");
      }   
      catch (IOException) {
         Console.WriteLine("An I/O error has occurred.");
      }
      catch (OutOfMemoryException) {
         Console.WriteLine("There is insufficient memory to read the file.");   
      }
      finally {
         if (sr != null) sr.Dispose();     
      }
   }
}
~~~

You can follow this basic pattern if you choose to implement or must implement a try/finally block, because your programming language doesn't support a using statement but does allow direct calls to the Dispose method.

try/finally 블럭을 구현하기를 선택하거나 해야한다면 이 기본 패턴을 따라 할 수 있습니다. 당신의 프로그래밍 언어는 using문을 지원하지 않기만 Dispose 메서드를 직접 호출할 수 있기때문입니다.