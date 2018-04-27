---
title: implementing_a_dispose_method
tags:
---
You implement a Dispose method to release unmanaged resources used by your application. The .NET garbage collector does not allocate or release unmanaged memory.

어플리케이션에서 사용되는 비관리 리소스를 해제하기위해 Dispose 메서드를 구현합니다. 닷넷 가비지 수집기는 비관리 메모리를 할당하거나 해제하지 않습니다.

The pattern for disposing an object, referred to as a dispose pattern, imposes order on the lifetime of an object. The dispose pattern is used only for objects that access unmanaged resources, such as file and pipe handles, registry handles, wait handles, or pointers to blocks of unmanaged memory. This is because the garbage collector is very efficient at reclaiming unused managed objects, but it is unable to reclaim unmanaged objects.

처리 패턴이라고 하는 객체 처리를 위한 패턴은 객체의 생명주기에 순서를 정합니다. 처리 패턴은 비관리 리소르에 접근하는 객체에 대해서만 사용됩니다. 가비지 수집기는 사용되지 않는 관리 객체를 회수하는데 매우 효과적이기 때문입니다. 그러나 비관리 객체는 회수할 수 없습니다.

The dispose pattern has two variations:

처리 패턴은 두가지 변형이 있습니다.

* You wrap each unmanaged resource that a type uses in a safe handle (that is, in a class derived from System.Runtime.InteropServices.SafeHandle). In this case, you implement the IDisposable interface and an additional Dispose(Boolean) method. This is the recommended variation and doesn't require overriding the Object.Finalize method.

    > Note
    >
    > The Microsoft.Win32.SafeHandles namespace provides a set of classes derived from SafeHandle, which are listed in the Using safe handles section. If you can't find a class that is suitable for releasing your unmanaged resource, you can implement your own subclass of SafeHandle.

* You implement the IDisposable interface and an additional Dispose(Boolean) method, and you also override the Object.Finalize method. You must override Finalize to ensure that unmanaged resources are disposed of if your IDisposable.Dispose implementation is not called by a consumer of your type. If you use the recommended technique discussed in the previous bullet, the System.Runtime.InteropServices.SafeHandle class does this on your behalf.

* 각 safe handle에서 사용하는 형식 비관리 리소스를 감쌉니다. 이 경우 IDisposable 인터페이스와 추가적인 Dispose(Boolean) 메서드를 구현합니다. 

    > 주의
    >
    > Microsoft.Win32.SafeHandles namespace는 safe handles 섹션에 나열되어 있는 SafeHandle에서 파생된 클래스 집합을 제공합니다. 비관리 리소스 해체를 위한 적합한 클래스를 찾을 수 없다면 당신 자신의 SafeHandle의 서브클래스를 구현할 수 있습니다.

* IDispose 인터페이스와 추가적인 Dispose(Boolean) 메서드를 구현합니다. 그리고 Object.Finalize 메서드를 재정의합니다. IDisposable.Dispose 구현이 호출되지 않으면 비관리 리소스가 처리되는 것을 확인하기 위해 Finalize를 재정의 해야합니다. 이전 글머리 기호에서 논한 권장 기술을 사용한다면 System.Runtime.InteropServices.SafeHandle 클래스는 당신 대신 이것을 수행합니다.

To help ensure that resources are always cleaned up appropriately, a Dispose method should be callable multiple times without throwing an exception.

리소스가 항상 적절하게 청소되는 것을 보장하기 위해 Dispose 메서드는 예외 없이 여러번 호출될 수 있어야 합니다.

The code example provided for the GC.KeepAlive method shows how aggressive garbage collection can cause a finalizer to run while a member of the reclaimed object is still executing. It is a good idea to call the KeepAlive method at the end of a lengthy Dispose method.

GC.KeepAlive 메서드에 제공된 코드예제는 재생된 객체의 멤버가 아직 실행중일때 적극적인 가비지 수집으로 인해 fianlizer가 실행될 수 있는 방법을 보여줍니다. 장황한 Dispose method 끝에서 KeepAlive 메서드를 호출하는 것은 좋은 생각입니다.

## Dispose() and Dispose(Boolean)
The IDisposable interface requires the implementation of a single parameterless method, Dispose. However, the dispose pattern requires two Dispose methods to be implemented:

IDisposable 인터페이스는 매개변수가 없는 단일 메서드의 구현이 필요합니다. 그러나 처리 패턴은 구현되어야 할 두개의 처리 메서드가 필요합니다.

* A public non-virtual (NonInheritable in Visual Basic) IDisposable.Dispose implementation that has no parameters.

* A protected virtual (Overridable in Visual Basic) Dispose method whose signature is:

    ~~~
    protected virtual void Dispose(bool disposing)
    ~~~

### The Dispose() overload
Because the public, non-virtual (NonInheritable in Visual Basic), parameterless Dispose method is called by a consumer of the type, its purpose is to free unmanaged resources and to indicate that the finalizer, if one is present, doesn't have to run. Because of this, it has a standard implementation:

공용, 비가상, 매개변수가 없는 처리 메서드는 형식의 소비자에 의해 호출되기 때문에 목적은 비관리 리소스와 해체하고 종료자가 존재하면 실행될 필요 없다는 것을 나타내는 것입니다. 

~~~
public void Dispose()
{
   // Dispose of unmanaged resources.
   Dispose(true);
   // Suppress finalization.
   GC.SuppressFinalize(this);
}
~~~

The Dispose method performs all object cleanup, so the garbage collector no longer needs to call the objects' Object.Finalize override. Therefore, the call to the SuppressFinalize method prevents the garbage collector from running the finalizer. If the type has no finalizer, the call to GC.SuppressFinalize has no effect. Note that the actual work of releasing unmanaged resources is performed by the second overload of the Dispose method.

처리메서드는 모든 객체 청소를 수행합니다. 그래서 가비지 수집기는 객체의 Object.Finalize 재정의를 호출할 필요가 없습니다. 그러므로 SuppressFinalize 메서드 호출은 가비지 수집기가 종료자를 실행하는것을 방지합니다. 형식에 종료자가 없다면 GC.SuppressFinalize 호출은 효과가 없습니다. 비관리 리소스의 처리 실제 작업은 처리 메서드의 두번째 오버로드에 의해 수행됩니다.

### The Dispose(Boolean) overload
In the second overload, the disposing parameter is a Boolean that indicates whether the method call comes from a Dispose method (its value is true) or from a finalizer (its value is false).

두번째 오버로드에서 disposing 매개변수는 메서드 호출이 처리메서드에서 오는지 종료자에서 오는지 나타내는 Boolean입니다.

The body of the method consists of two blocks of code:

메서드의 본문은 두개의 블럭으로 구성 됩니다.

* A block that frees unmanaged resources. This block executes regardless of the value of the disposing parameter.

    비관리 리소스를 해제하는 블록. 이 블록은 disposing 매개변수의 값과 상관없이 실행합니다.

* A conditional block that frees managed resources. This block executes if the value of disposing is true. The managed resources that it frees can include:

    관리 리소스를 해제하는 조건부 블럭. 이 블럭은 disposing의 값이 true이면 실행합니다. 해제하는 관리 리소스는 포함할 수 있습니다.

    Managed objects that implement IDisposable. The conditional block can be used to call their Dispose implementation. If you have used a safe handle to wrap your unmanaged resource, you should call the SafeHandle.Dispose(Boolean) implementation here.

    IDisposable을 구현하는 관리 객체입니다. 조건부 블럭은 처리 구현을 호출하는데 사용될 수 있습니다. 비관리 리소스를 감싸는데 safe handle을 사용한다면 SafeHanlde.Dispose(Boolean) 구현을 여기서 호출해야 합니다.

    Managed objects that consume large amounts of memory or consume scarce resources. Freeing these objects explicitly in the Dispose method releases them faster than if they were reclaimed non-deterministically by the garbage collector.

    많은 양의 메모리나 부족한 리소스를 소비하는 관리 객체. 처리 메서드에서 이러한 객체를 분명하게 해제하면 가비지 수집기가 비 결적적으로 회수하는 것보다 빠르게 풀어줍니다.

If the method call comes from a finalizer (that is, if disposing is false), only the code that frees unmanaged resources executes. Because the order in which the garbage collector destroys managed objects during finalization is not defined, calling this Dispose overload with a value of false prevents the finalizer from trying to release managed resources that may have already been reclaimed.

종료자에서 메서드 호출이 생산되면 비관리 비소스를 해제하는 코드만 실행됩니다. 가비지 수집기가 관리 객체를 파괴하는 명령은 종료중에 정의 되지 않습니다. false 값을 지닌 이 처리 오버로드를 호출하면 종료자가 이미 회수되어 있는 관리 리소스를 해제하려는 것을 방지합니다.

## Implementing the dispose pattern for a base class
If you implement the dispose pattern for a base class, you must provide the following:

기본 클래스에 대한 처리 패턴을 구현하려면 다음을 제공해야 합니다.

> Important
>
> You should implement this pattern for all base classes that implement Dispose() and are not sealed (NotInheritable in Visual Basic).
> Dispose()를 구현하고 보호되지 않은 모든 기본 클래스에 대해 이 패턴을 구현 해야 합니다.

* A Dispose implementation that calls the Dispose(Boolean) method.
* Dispose(Boolean) 메서드를 호출하는 처리 구현

* A Dispose(Boolean) method that performs the actual work of releasing resources.
* 리소스 해제 실제 작업을 수행하는 Dispose(Boolean) 메서드

* Either a class derived from SafeHandle that wraps your unmanaged resource (recommended), or an override to the Object.Finalize method. The SafeHandle class provides a finalizer that frees you from having to code one.
* 비관리 리소스를 감싸는 SafeHandle에서 파생된 클래스 또는 Object.Finalize 메서드를 재정의. SafeHandle class는 코드를 작성하지 않아도 되는 종료자를 제공합니다.

Here's the general pattern for implementing the dispose pattern for a base class that uses a safe handle.

~~~
using Microsoft.Win32.SafeHandles;
using System;
using System.Runtime.InteropServices;

class BaseClass : IDisposable
{
   // Flag: Has Dispose already been called?
   bool disposed = false;
   // Instantiate a SafeHandle instance.
   SafeHandle handle = new SafeFileHandle(IntPtr.Zero, true);
   
   // Public implementation of Dispose pattern callable by consumers.
   public void Dispose()
   { 
      Dispose(true);
      GC.SuppressFinalize(this);           
   }
   
   // Protected implementation of Dispose pattern.
   protected virtual void Dispose(bool disposing)
   {
      if (disposed)
         return; 
      
      if (disposing) {
         handle.Dispose();
         // Free any other managed objects here.
         //
      }
      
      // Free any unmanaged objects here.
      //
      disposed = true;
   }
}
~~~

> Note
>
> The previous example uses a SafeFileHandle object to illustrate the pattern; any object derived from SafeHandle could be used instead. Note that the example does not properly instantiate its SafeFileHandle object.

Here's the general pattern for implementing the dispose pattern for a base class that overrides Object.Finalize.

~~~
using System;

class BaseClass : IDisposable
{
   // Flag: Has Dispose already been called?
   bool disposed = false;
   
   // Public implementation of Dispose pattern callable by consumers.
   public void Dispose()
   { 
      Dispose(true);
      GC.SuppressFinalize(this);           
   }
   
   // Protected implementation of Dispose pattern.
   protected virtual void Dispose(bool disposing)
   {
      if (disposed)
         return; 
      
      if (disposing) {
         // Free any other managed objects here.
         //
      }
      
      // Free any unmanaged objects here.
      //
      disposed = true;
   }

   ~BaseClass()
   {
      Dispose(false);
   }
}
~~~

> Note
>
> In C#, you override Object.Finalize by defining a destructor.

## Implementing the dispose pattern for a derived class
A class derived from a class that implements the IDisposable interface shouldn't implement IDisposable, because the base class implementation of IDisposable.Dispose is inherited by its derived classes. Instead, to implement the dispose pattern for a derived class, you provide the following:

IDisposable 인터페이스를 구현하는 클래스에서 파생된 클래스는 IDisposable을 구현할 수 없습니다. IDisposable.Dipose의 기본 구현클래스는 파생된 클래스에 의해 상속됩니다. 대신 파생된 클래스에 대해 처리 패턴을 구현하기 위해 다음을 제공합니다.

* A protected Dispose(Boolean) method that overrides the base class method and performs the actual work of releasing the resources of the derived class. This method should also call the Dispose(Boolean) method of the base class and pass it a value of true for the disposing argument.
* 기본 클래스 메서드를 재정의하고 파생된 클래스의 리소스를 해제하는 실제 작업을 수행하는 protected Dispose(Boolean) 메서드. 이 메서드는 기본 클래스의 Dispose(Boolean) 메서드를 호출하고 disposing 인수에 true 값을 전달해야 합니다.

* Either a class derived from SafeHandle that wraps your unmanaged resource (recommended), or an override to the Object.Finalize method. The SafeHandle class provides a finalizer that frees you from having to code one. If you do provide a finalizer, it should call the Dispose(Boolean) overload with a disposing argument of false.
* 비관리 리소스를 감싸는 SafeHandel에서 파생된 클래스 또는 Object.Finalize 메서드를 재정의. SafeHandle 클래스는 코드를 작성하지 않아도 되는 종료자를 제공합니다. false인 disposing 인수를 가진 Dispose(Boolean) 오버로드를 호출해야 합니다.

Here's the general pattern for implementing the dispose pattern for a derived class that uses a safe handle:

~~~
using Microsoft.Win32.SafeHandles;
using System;
using System.Runtime.InteropServices;

class DerivedClass : BaseClass
{
   // Flag: Has Dispose already been called?
   bool disposed = false;
   // Instantiate a SafeHandle instance.
   SafeHandle handle = new SafeFileHandle(IntPtr.Zero, true);

   // Protected implementation of Dispose pattern.
   protected override void Dispose(bool disposing)
   {
      if (disposed)
         return; 
      
      if (disposing) {
         handle.Dispose();
         // Free any other managed objects here.
         //
      }
      
      // Free any unmanaged objects here.
      //

      disposed = true;
      // Call base class implementation.
      base.Dispose(disposing);
   }
}
~~~

> Note
>
> The previous example uses a SafeFileHandle object to illustrate the pattern; any object derived from SafeHandle could be used instead. Note that the example does not properly instantiate its SafeFileHandle object.

Here's the general pattern for implementing the dispose pattern for a derived class that overrides Object.Finalize:

~~~
using System;

class DerivedClass : BaseClass
{
   // Flag: Has Dispose already been called?
   bool disposed = false;
   
   // Protected implementation of Dispose pattern.
   protected override void Dispose(bool disposing)
   {
      if (disposed)
         return; 
      
      if (disposing) {
         // Free any other managed objects here.
         //
      }
      
      // Free any unmanaged objects here.
      //
      disposed = true;
      
      // Call the base class implementation.
      base.Dispose(disposing);
   }

   ~DerivedClass()
   {
      Dispose(false);
   }
}
~~~

> Note
>
> In C#, you override Object.Finalize by defining a destructor.

## Using safe handles
Writing code for an object's finalizer is a complex task that can cause problems if not done correctly. Therefore, we recommend that you construct System.Runtime.InteropServices.SafeHandle objects instead of implementing a finalizer.

Classes derived from the System.Runtime.InteropServices.SafeHandle class simplify object lifetime issues by assigning and releasing handles without interruption. They contain a critical finalizer that is guaranteed to run while an application domain is unloading. For more information about the advantages of using a safe handle, see System.Runtime.InteropServices.SafeHandle. The following derived classes in the Microsoft.Win32.SafeHandles namespace provide safe handles:

* The SafeFileHandle, SafeMemoryMappedFileHandle, and SafePipeHandle class, for files, memory mapped files, and pipes.

* The SafeMemoryMappedViewHandle class, for memory views.

* The SafeNCryptKeyHandle, SafeNCryptProviderHandle, and SafeNCryptSecretHandle classes, for cryptography constructs.

* The SafeRegistryHandle class, for registry keys.

* The SafeWaitHandle class, for wait handles.

## Using a safe handle to implement the dispose pattern for a base class
The following example illustrates the dispose pattern for a base class, DisposableStreamResource, that uses a safe handle to encapsulate unmanaged resources. It defines a DisposableResource class that uses a SafeFileHandle to wrap a Stream object that represents an open file. The DisposableResource method also includes a single property, Size, that returns the total number of bytes in the file stream.

~~~
using Microsoft.Win32.SafeHandles;
using System;
using System.IO;
using System.Runtime.InteropServices;

public class DisposableStreamResource : IDisposable
{
   // Define constants.
   protected const uint GENERIC_READ = 0x80000000;
   protected const uint FILE_SHARE_READ = 0x00000001;
   protected const uint OPEN_EXISTING = 3;
   protected const uint FILE_ATTRIBUTE_NORMAL = 0x80;
   protected IntPtr INVALID_HANDLE_VALUE = new IntPtr(-1);
   private const int INVALID_FILE_SIZE = unchecked((int) 0xFFFFFFFF);
   
   // Define Windows APIs.
   [DllImport("kernel32.dll", EntryPoint = "CreateFileW", CharSet = CharSet.Unicode)]
   protected static extern IntPtr CreateFile (
                                  string lpFileName, uint dwDesiredAccess, 
                                  uint dwShareMode, IntPtr lpSecurityAttributes, 
                                  uint dwCreationDisposition, uint dwFlagsAndAttributes, 
                                  IntPtr hTemplateFile);
   
   [DllImport("kernel32.dll")]
   private static extern int GetFileSize(SafeFileHandle hFile, out int lpFileSizeHigh);
    
   // Define locals.
   private bool disposed = false;
   private SafeFileHandle safeHandle; 
   private long bufferSize;
   private int upperWord;
   
   public DisposableStreamResource(string filename)
   {
      if (filename == null)
         throw new ArgumentNullException("The filename cannot be null.");
      else if (filename == "")
         throw new ArgumentException("The filename cannot be an empty string.");
            
      IntPtr handle = CreateFile(filename, GENERIC_READ, FILE_SHARE_READ,
                                 IntPtr.Zero, OPEN_EXISTING, FILE_ATTRIBUTE_NORMAL,
                                 IntPtr.Zero);
      if (handle != INVALID_HANDLE_VALUE)
         safeHandle = new SafeFileHandle(handle, true);
      else
         throw new FileNotFoundException(String.Format("Cannot open '{0}'", filename));
      
      // Get file size.
      bufferSize = GetFileSize(safeHandle, out upperWord); 
      if (bufferSize == INVALID_FILE_SIZE)
         bufferSize = -1;
      else if (upperWord > 0) 
         bufferSize = (((long)upperWord) << 32) + bufferSize;
   }
   
   public long Size 
   { get { return bufferSize; } }

   public void Dispose()
   {
      Dispose(true);
      GC.SuppressFinalize(this);
   }           

   protected virtual void Dispose(bool disposing)
   {
      if (disposed) return;

      // Dispose of managed resources here.
      if (disposing)
         safeHandle.Dispose();
      
      // Dispose of any unmanaged resources not wrapped in safe handles.
      
      disposed = true;
   }  
}
~~~

## Using a safe handle to implement the dispose pattern for a derived class
The following example illustrates the dispose pattern for a derived class, DisposableStreamResource2, that inherits from the DisposableStreamResource class presented in the previous example. The class adds an additional method, WriteFileInfo, and uses a SafeFileHandle object to wrap the handle of the writable file.

~~~
using Microsoft.Win32.SafeHandles;
using System;
using System.IO;
using System.Runtime.InteropServices;
using System.Threading;

public class DisposableStreamResource2 : DisposableStreamResource
{
   // Define additional constants.
   protected const uint GENERIC_WRITE = 0x40000000; 
   protected const uint OPEN_ALWAYS = 4;
   
   // Define additional APIs.
   [DllImport("kernel32.dll")]   
   protected static extern bool WriteFile(
                                SafeFileHandle safeHandle, string lpBuffer, 
                                int nNumberOfBytesToWrite, out int lpNumberOfBytesWritten,
                                IntPtr lpOverlapped);
   
   // Define locals.
   private bool disposed = false;
   private string filename;
   private bool created = false;
   private SafeFileHandle safeHandle;
   
   public DisposableStreamResource2(string filename) : base(filename)
   {
      this.filename = filename;
   }
   
   public void WriteFileInfo()
   { 
      if (! created) {
         IntPtr hFile = CreateFile(@".\FileInfo.txt", GENERIC_WRITE, 0, 
                                   IntPtr.Zero, OPEN_ALWAYS, 
                                   FILE_ATTRIBUTE_NORMAL, IntPtr.Zero);
         if (hFile != INVALID_HANDLE_VALUE)
            safeHandle = new SafeFileHandle(hFile, true);
         else
            throw new IOException("Unable to create output file.");

         created = true;
      }

      string output = String.Format("{0}: {1:N0} bytes\n", filename, Size);
      int bytesWritten;
      bool result = WriteFile(safeHandle, output, output.Length, out bytesWritten, IntPtr.Zero);                                     
   }

   protected new virtual void Dispose(bool disposing)
   {
      if (disposed) return;
      
      // Release any managed resources here.
      if (disposing)
         safeHandle.Dispose();
      
      disposed = true;
      
      // Release any unmanaged resources not wrapped by safe handles here.
      
      // Call the base class implementation.
      base.Dispose(true);
   }
}
~~~