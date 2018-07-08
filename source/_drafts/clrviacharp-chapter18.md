---
title: clrviacharp-chapter18
tags:
---
## 사용자 정의 특성의 사용
.NET Framework 클래스 라이브러리 에서는 사용 가능한 수백 개의 사용자 정의 특성들이 정의되어 있다.
* DllImport 특성을 메서드에 적용하여 CLR은 메서드의 실제 구현이 다른 DLL에 들어있는 비관리 코드에 있음을 이해하고 연결해준다.
* Serializable 특성을 임의의 타입에 적용하여 serialization 포맷터를 통해 특정 인스턴스 필드를 serialize하거나 deserialize한다.
* AssemblyVersion 특성을 어셈블리에 적용하여 어셈블리의 버전 정보를 관리한다.
* Flags 특성을 열거 타입에 적용하여 열거 타입이 비트 플래그의 집합처럼 사용될 수 있게 해준다.

다음은 다양한 사용자 정의 특성을 사용하느 C# 코드의 예다.
~~~
using System;
using System.Runtime.InteropServices;

[StructLayout(LayoutKind.Sequential, CharSet = CharSet.Auto)]
internal sealed class OSVERSIONINFO {
    public OSVERSIONINFO()
    {
        OSVersionInfoSize = (UInt32)Marshal.SizeOf(this);
    }

    public UInt32 OSVersionInfoSize = 0;
    public UInt32 MajorVersion = 0;
    public UInt32 MinorVersion = 0;
    public UInt32 BuildNumber = 0;
    public UInt32 PlatformId = 0;

    [MarshalAs(UnmanagedType.ByValTStr, SizeConst = 128)]
    public String CSDVersion = null;
}

internal sealed class MyClass
{
    [DllImport("Kernel32", CharSet = CharSet.Auto, SetLastError = true)]
    public static extern Boolean GetVersionEx([In, Out] OSVERSIONINFO ver);
}
~~~
사용자 정의 특성이란 단순히 특정 타입의 인스턴스다. CLS의 규약에 따르면, 사용자 정의 특성 타입은 직접적이든 간접적이든 모두 public 추상 클래스인 System.Attribute 클래스로부터 상속을 받아야 한다고 되어 있다.
~~~
[DllImport("Kernel32", CharSet = CharSet.Auto, SetLastError = true)]
~~~
이 문법은 일반적인 생성자 호출 문법과는 많이 다르다는 것을 알 수 있따. DllImportAttribute 클래스에 대한 문서를 살펴보면, 생성자는 단일 String 매개변수 하나를 필요로 한다는 것을 알 수 있다. 즉 앞의 예제에서 "Kernel32"는 생성자의 매개변수로서 전달된 것이다. 생성자의 매개변수들은 보통 **위치 매개변수**(Positional Parameter)라고 부르며 누락할 수 없는 필수 매개변수들이므로 특성을 적용하려 할 때 반드시 지정해야만 한다.

필드나 속성등에 값을 대입하기 위하여 지정하는 매개변수를 위치 매개변수와 구분하여 **명명된 매개변수**(Named Parameters)라고 부르며 선택적으로 사용할 수 있다.

## 사용자 정의 특성 클래스 정의하기
만약 여러분이 마이크로소프트의 직원이고, 열거 타입에 대한 비트 플래그 지원 기능을 구현해야 한다고 해보자. 이 목표를 달성하기 위해서는 우선 FlagsAttribute 클래스를 새로 정의해야 할 것이다.
~~~
namespace System {
    public class FlagsAttribute : System.Attribute {
        public FlagsAttribute() {

        }
    }
}
~~~
클래스의 이름 뒤에 붙는 접미사가 Attribute로 되어 있는데, 필수사항이 아닌 표준 권고사항이다. 마지막으로, 모든 비추상 특성(non-abstract attribute)은 반드시 하나 이상의 public 생성자를 가져야 한다.

> **중요**: 사용자 정의 특성을 정의할 때에는 반드시 이를 논리적 상태 컨테이너로서 생각해야 한다. 사용자 정의 특성은 가능한 간단한 형태의 클래스 타입으로 유지하는 것이 좋다. 이 클래스는 반드시 하나의 public 생성자를 제공해야 하고 사용자 정의 특성의 의미를 완성하기 위해서 꼭 필요한 매개변수를 받아야 하며, 하나 이상의 public 필드나 속성을 추가하여 특성의 부수적 사항을 기재할 수 있도록 해야 한다. 그 외에 public 메서드나 이벤트, 혹은 다른 멤버들을 제공하지 말아야 한다.

> 보통, 필자는 어떤 경우에서건 public 필드의 사용을 권장하지 않으며, 사용자 정의 특성에 있어서도 마찬가지다. 이보다 속성을 사용하는 것이 훨씬 더 나은데 왜냐하면 사용자 정의 특성 클래스가 어덯게 구현되는가에 따라 그 내용이 바뀌는 경우에도 좀 더 유연하게 대처할 수 있기 때문이다.

지금까지는 FlagsAttribute 클래스의 인스턴스가 어느 대상이든 적용이 가능한 상태이지만, 이 특성을 열거 타입에 한해서만 적용이 가능하도록 제한할 필요가 있다.
~~~
namespace System {
    [AttributeUsage(AttributeTargets.Enum, Inherited = false)]
    public class FlagsAttribute : System.Attribute {
        public FlagsAttribute() {

        }
    }
}
~~~
이 예제에서는 AttributeUsageUsageAttribute 사용자 정의 특성을 사용하여 적용 대상을 제한하였다. 결국 사용자 정의 특성 그 자체도 클래스이며, 다른 특성의 적용을 받을 수 있는 클래스인 셈이다. AttributeUsage 사용자 정의 특성은 단순한 클래스로 컴파일러에 새롭게 정의하는 사용자 정의 특성이 어디에 적용되어야 하는지를 알려주는 역할을 수행한다.

AttributeUsageAttribute 특성의 또 다른 속성인 Inherited는 적용 대상이 클래스일 경우에는 그 클래스를 상속한 클래스에도 동일 특성이 적용됨을 의미하며, 대상이 메서드일 경우에도는 이 메서드를 재정의하는 경우에도 동일 특성이 적용됨을 의미한다.
~~~
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited=true)]
internal class TastyAttribute : Attribute {

}

[Tasty][Serializable]
internal class BaseType {
    [Tasty]
    protected virtual void DoSomething() { }
}

internal class DerivedType : BaseType {
    protected override void DoSomething() { }
}