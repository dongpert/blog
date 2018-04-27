---
title: delegate
tags:
---
The declaration of a delegate type is similar to a method signature. It has a return value and any number of parameters of any type:

델리게이트 형식 선언은 메서드 시그니처와 유사합니다. 반환값과 모든 유형의 많은 매개변수를 가지고 있습니다.

~~~
public delegate void TestDelegate(string message);  
public delegate int TestDelegate(MyType m, long num);  
~~~

A delegate is a reference type that can be used to encapsulate a named or an anonymous method. Delegates are similar to function pointers in C++; however, delegates are type-safe and secure. For applications of delegates, see [Delegates](https://docs.microsoft.com/ko-kr/dotnet/csharp/programming-guide/delegates/index) and [Generic Delegates](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/generic-delegates).

델리게이트는 명명 또는 익명 메서드를 캡슐화를 위해 사용될 수 있는 참조 형식입니다. 델리게이트는 C++에서 함수 포인터와 유사합니다. 그러나 델리게이트는 type-safe와 안전합니다. 

## Remarks
Delegates are the basis for Events.

델리게이트는 이벤트를 위한 기초입니다.

A delegate can be instantiated by associating it either with a named or anonymous method. For more information, see [Named Methods](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/delegates-with-named-vs-anonymous-methods) and [Anonymous Methods](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-methods).

델리게이트는 이름이나 무명메서드와 연결하여 인스턴스화 될수 있습니다. 더 많은 정보를 위해 명명된 메서드와 무명 메서드를 보십시오.

The delegate must be instantiated with a method or lambda expression that has a compatible return type and input parameters. For more information on the degree of variance that is allowed in the method signature, see [Variance in Delegates](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/using-variance-in-delegates). For use with anonymous methods, the delegate and the code to be associated with it are declared together. Both ways of instantiating delegates are discussed in this section.

델리게이트는 적합한 반환 형식과 입력 매개변수가 있는 메서드나 람다 표현식으로 인스턴스화 되어야 합니다. 메서드 시그니처에 허용되는 가변성 정도에 관한 더 많은 정보 델리게이트에 가변성을 보십시요. 익명 메서드를 사용하기 위해서 델리게이트와 관련된 코드가 함께 정의되어야 합니다. 델리게이트를 인스터스화 하는 두가지 방법이 이 섹션에서 논의될 것 입니다.

## Example
~~~
// Declare delegate -- defines required signature:
delegate double MathAction(double num);

class DelegateTest
{
    // Regular method that matches signature:
    static double Double(double input)
    {
        return input * 2;
    }

    static void Main()
    {
        // Instantiate delegate with named method:
        MathAction ma = Double;

        // Invoke delegate ma:
        double multByTwo = ma(4.5);
        Console.WriteLine("multByTwo: {0}", multByTwo);

        // Instantiate delegate with anonymous method:
        MathAction ma2 = delegate(double input)
        {
            return input * input;
        };

        double square = ma2(5);
        Console.WriteLine("square: {0}", square);

        // Instantiate delegate with lambda expression
        MathAction ma3 = s => s * s * s;
        double cube = ma3(4.375);

        Console.WriteLine("cube: {0}", cube);
    }
    // Output:
    // multByTwo: 9
    // square: 25
    // cube: 83.740234375
}
~~~

[원문](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/delegate)