---
title: netguide-implementing-a-dispose-method
tags:
---
You implement a Dispose method to release unmanaged resources used by your application. The .NET garbage collector does not allocate or release unmanaged memory.

애플리케이션이 사용하는 관리되지 않은 리소스를 해체하기 위해 Dispose 메서드를 구현합니다. 닷넷 가비지 수집기는 관리되지 않는 메모리를 할당하거나 해체하지 않습니다.

The pattern for disposing an object, referred to as a dispose pattern, imposes order on the lifetime of an object. The dispose pattern is used only for objects that access unmanaged resources, such as file and pipe handles, registry handles, wait handles, or pointers to blocks of unmanaged memory. This is because the garbage collector is very efficient at reclaiming unused managed objects, but it is unable to reclaim unmanaged objects.

삭제 패턴 이라고 하는 객체 삭제를 위한 패턴은 객체의 수명에 순서를 적용합니다. 삭제 패턴은 비관리 리소스에 접근하는 객체만을 위해 사용됩니다. 이것은 가비지 수집기가 비사용 관리 객체를 회수하는데 매우 효율적이지만 비관리 객체는 회수할 수 없기 때문입니다.

The dispose pattern has two variations:
* You wrap each unmanaged resource that a type uses in a safe handle (that is, in a class derived from System.Runtime.InteropServices.SafeHandle). In this case, you implement the IDisposable interface and an additional Dispose(Boolean) method. This is the recommended variation and doesn't require overriding the Object.Finalize method.

* 형식이 safe handle에서 사용하는 각 비관리 리소를 감쌉니다. 이 경우 IDisposable 인터페이스와 추가 Dispose 메서드를 구현합니다. 이것은 추천되는 변형이고 Object.Finalize 메서드를 재정의할 필요가 없습니다.

* You implement the IDisposable interface and an additional Dispose(Boolean) method, and you also override the Object.Finalize method. You must override Finalize to ensure that unmanaged resources are disposed of if your IDisposable.Dispose implementation is not called by a consumer of your type. If you use the recommended technique discussed in the previous bullet, the System.Runtime.InteropServices.SafeHandle class does this on your behalf.

IDisposable 인터페이스와 추가적인 Dispose 메서드를 구현하고, 또한 Object.Finalize method를 재정의합니다. IDisposable.Dispose 구현이 형식의 소비자에 의해 호출되지 않으면 비관리 리소스가 삭제되게 하기 위해 Finalize를 재정의해야 합니다. 이전 불릿에서 논의한 추천되는 기술을 사용한다면 System.Runtime.InteropServices.SafeHandle 클래스는 이것을 대신 합니다.

To help ensure that resources are always cleaned up appropriately, a Dispose method should be callable multiple times without throwing an exception.

리소스가 항상 적절하게 청소되는 것을 돕기위해 Dispose 메서드는 예외를 던지지 않고 여러번 호출될 수 있어야만 합니다.

The code example provided for the GC.KeepAlive method shows how aggressive garbage collection can cause a finalizer to run while a member of the reclaimed object is still executing. It is a good idea to call the KeepAlive method at the end of a lengthy Dispose method.

C.KeepAlive 메서드에 대해 제공되는 코드 예제는 방법을 적극적인 가비지 수집으로 재생된 객체의 멤버가 아직 실행중일때 종료자가 실행될 수 있는 방법을 보여줍니다. 긴 Dispose 메서드 끝에서 KeepAlive 메서드를 호출하는 것은 좋은 생각입니다.

## Dispose() and Dispose(Boolean)
The IDisposable interface requires the implementation of a single parameterless method, Dispose. However, the dispose pattern requires two Dispose methods to be implemented:

IDisposable 인터페이스는 단일 매개변수 없는 Dispose 메서드의 구현이 필요합니다. 그러나 삭제 패턴은 구현되어야 할 두개의 Dispose 메서드를 필요로 합니다.

* A public non-virtual (NonInheritable in Visual Basic) IDisposable.Dispose implementation that has no parameters.

* A protected virtual (Overridable in Visual Basic) Dispose method whose signature is:
~~~
protected virtual void Dispose(bool disposing)
~~~

### The Dispose() overload
Because the public, non-virtual (NonInheritable in Visual Basic), parameterless Dispose method is called by a consumer of the type, its purpose is to free unmanaged resources and to indicate that the finalizer, if one is present, doesn't have to run. Because of this, it has a standard implementation:

공용, 비가상, 매개변수가 없는 Dispose 메서드가 형식 소비자에 의해 호출되기 때문에, 비 관리 리소스를 해제하고 소멸자가 만약 존재한다면 실행할 필요가 없는 것을 나타내기 위한 것이 목적입니다. 
~~~
public void Dispose()
{
    // Dispose of unmanaged resources.
    Dispose(true);
    // Suppress finalization.
    GC.SuppressFinalize(this);
}
~~~