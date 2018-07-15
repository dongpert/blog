---
title: netguid-anonymous-functions
tags:
---
An anonymous function is an "inline" statement or expression that can be used wherever a delegate type is expected. You can use it to initialize a named delegate or pass it instead of a named delegate type as a method parameter.

익명 함수는 "인라인" 구문 또는 델리게이트 형식이 예상되는 곳에서 사용될 수 있는 표현식입니다. 명명된 델리게이트를 초기화하거나 메서드의 매개변수로 명명된 델리게이트 형식 대신 전달하기 위해 사용할 수 있습니다.

There are two kinds of anonymous functions, which are discussed individually in the following topics:

다음 주제에서 개별적으로 논의되는 익명 함수의 두 종류가 있습니다.
* Lambda Expressions
* Anonymous Method

> Lambda expressions can be bound to expression trees and also to delegates.
람다 식은 식 트리와 델리게이트에 바인딩 될 수 있습니다.

## The Evolution of Delegates in C#
In C# 1.0, you created an instance of a delegate by explicitly initializing it with a method that was defined elsewhere in the code. C# 2.0 introduced the concept of anonymous methods as a way to write unnamed inline statement blocks that can be executed in a delegate invocation. C# 3.0 introduced lambda expressions, which are similar in concept to anonymous methods but more expressive and concise. These two features are known collectively as anonymous functions. In general, applications that target version 3.5 and later of the .NET Framework should use lambda expressions.

C# 1.0에서 코드의 다른 위치에 정의된 메서드로 명시적으로 인스턴스화 해서 대리자의 인스턴스를 생성했습니다. C# 2.0은 대리자 호출에서 실행될 수 있는 무명의 인라인 구문 블럭을 작성하는 방식으로 익명 매서드의 개념을 소개했습니다. C# 3.0은 무명 메서드에 개념과 비슷하지만 더 표현이 풍부하고 간결한 람다 식을 소개했습니다. 두가지 특징은 집합적으로 익명 함수로 알려져 있습니다. 일반적으로 .NET Framework의 버전 3.5이상을 타겟으로 하는 애플리케이션은 람다식을 사용해야 합니다.

The following example demonstrates the evolution of delegate creation from C# 1.0 to C# 3.0:

다음 예제는 C# 1.0에서 C# 3.0까지 대리자의 생성의 발전을 보여줍니다.
~~~
class Test
{
    delegate void TestDelegate(string s);
    static void M(string s)
    {
        Console.WriteLine(s);
    }

    static void Mains(string[] args)
    {
        // Original delegate syntax required 
        // initialization with a named method.
        TestDelegate testDelA = new Delegate(M);

        // C# 2.0: A delegate can be initialized with
        // inline code, called an "anonymous method." This
        // method takes a string as an input parameter.
        TestDelegate testDelB = delegate(string s) { Console.WriteLine(s); }

        // C# 3.0. A delegate can be initialized with
        // a lambda expression. The lambda also takes a string
        // as an input parameter (x). The type of x is inferred by the compiler.
        TestDelegate testDelC = (x) => { Console.WriteLine; };

        testDelA("Hello. My name is M and I write lines.");
        testDelB("That's nothing. I'm anonymous and ");
        testDelC("I'm a famous author.");

        // Keep console window open in debug mode.
        Console.WriteLine("Press any key to exit.");
        Console.ReadKey();
    }
}
/* Output:
    Hello. My name is M and I write lines.
    That's nothing. I'm anonymous and
    I'm a famous author.
    Press any key to exit.
 */
 ~~~