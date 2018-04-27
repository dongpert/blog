---
title: delegates
tags:
---
A delegate is a type that represents references to methods with a particular parameter list and return type. When you instantiate a delegate, you can associate its instance with any method with a compatible signature and return type. You can invoke (or call) the method through the delegate instance.

델리게이트는 특정 매개변수와 반환값이 있는 메서드에 참조를 표현하는 형식입니다. 델리게이트를 인스턴스화할때 인스턴스를 호환이 되는 시그니처와 반환값이 있는 모든 메서드를 연계할 수 있습니다. 델리게이트 인스턴스를 통하여 메서드를 실행할 수 있습니다.

Delegates are used to pass methods as arguments to other methods. Event handlers are nothing more than methods that are invoked through delegates. You create a custom method, and a class such as a windows control can call your method when a certain event occurs. The following example shows a delegate declaration:

델리게이트는 메서드를 인수처럼 다른 메서드에 전달하는데 사용됩니다. 이벤트 핸들러는 델리게이트를 통하여 실행되는 메서드에 지나지 않습니다. 사용자 지정 메서드를 생성합니다 그리고 윈도우 컨트롤러와 같은 클래스는 특정 이벤트가 발생하면 당신의 메서드를 호출합니다. 다음 예제는 델리게이트 선언을 보여줍니다.

~~~
public delegate int PerformCalculation(int x, int y);
~~~

> Note
> 
> In the context of method overloading, the signature of a method does not include the return value. But in the context of delegates, the signature does include the return value. In other words, a method must have the same return type as the delegate.
> 메서드 오버로딩의 컨텍스트에서 메서드의 시그니처는 반환값을 포함하지 않습니다. 델리게이트 컨텍스트에서는 시그니처는 반환값을 포함합니다. 다시말해서 메서드는 델리게이트처럼 값은 반환값을 가져야만 합니다.

This ability to refer to a method as a parameter makes delegates ideal for defining callback methods. For example, a reference to a method that compares two objects could be passed as an argument to a sort algorithm. Because the comparison code is in a separate procedure, the sort algorithm can be written in a more general way.

매개변수로 메서드를 참조하는 이 기능은 대리자가 콜백 메서드를 정의하는데 이상적이게 합니다. 예를 들어 두 객체를 비교하는 메서드에 대한 참조는 인수로 정렬 알고리즘이 전달될 수 있습니다.
비교코드는 분리된 과정에 있기 때문에 정렬 알고리즘은 더 일반적인 방법으로 작성될 수 있습니다.

## Delegates Overview
Delegates have the following properties:

* Delegates are like C++ function pointers but are type safe.
* Delegates allow methods to be passed as parameters.
* Delegates can be used to define callback methods.
* Delegates can be chained together; for example, multiple methods can be called on a single event.
* Methods do not have to match the delegate type exactly. For more information, see Using Variance in Delegates.
* C# version 2.0 introduced the concept of Anonymous Methods, which allow code blocks to be passed as parameters in place of a separately defined method. C# 3.0 introduced lambda expressions as a more concise way of writing inline code blocks. Both anonymous methods and lambda expressions (in certain contexts) are compiled to delegate types. Together, these features are now known as anonymous functions. For more information about lambda expressions, see Anonymous Functions.

대리자는 다음의 속성을 가지고 있습니다.

* 대리자는 C++ 함수 포인터와 같다 그러나 형식에 안전합니다.
* 대리자는 매서드가 매개변수로 전달되는 것을 허용합니다.
* 대리자는 콜백 메서드로 정의되어 사용될 수 있습니다.
* 대리자는 함께 묶일수 있습니다. 예를 들어 여러개의 메서드는 단일 이벤트에서 호출될 수 있습니다.
* 메서드는 대리자 형식과 정확히 일치시킬 필요가 없습니다.
* C# 2.0 버전은 별도의 정의된 메서드에서 매개변수처럼 전달되는 코드블럭을 허용하는 익명 메서드의 개념을 도입했습니다. C# 3.0은 내부코드블럭을 작성하는 더 간결한 방법으로 람다표현식을 도입했습니다. 익명 메서드와 람다 표현식은 델리게이트 형식으로 컴파일 됩니다. 이러한 특징들은 익명 기능으로 알려져 있습니다.

## In This Section
* [Using Delegates](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/using-delegates)
* When to Use Delegates Instead of Interfaces (C# Programming Guide)
* Delegates with Named vs. Anonymous Methods
* Anonymous Methods
* Using Variance in Delegates
* How to: Combine Delegates (Multicast Delegates)
* How to: Declare, Instantiate, and Use a Delegate