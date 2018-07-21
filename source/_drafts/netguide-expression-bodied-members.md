---
title: netguide-expression-bodied-members
tags:
---
Expression body definitions let you provide a member's implementation in a very concise, readable form. You can use an expression body definition whenever the logic for any supported member, such as a method or property, consists of a single expression. An expression body definition has the following general syntax:

식 본문 정의는 매우 간결하고, 알아보기 쉬운 형태로 멤버 구현을 제공할 수 있습니다. 메서드 또는 속성과 같은 모든 지원되는 멤버에 대한 논리가 단일 식으로 구성될 때마다 식 본문 정의를 사용할 수 있습니다. 식 본문 정의는 다음 일반 구문이 있습니다.
~~~
member => expression;
~~~
where expression is a valid expression.

where 식은 유효한 식입니다.

Support for expression body definitions was introduced for methods and property get accessors in C# 6 and was expanded in C# 7.0. Expression body definitions can be used with the type members listed in the following table:

식 본문 정의에 대한 지원은 C# 6 에서 메서드와 속성 get 접근자에 대해 소개되었고 C# 7.0에서 확장 되었습니다. 식 본문 정의는 다음 테이블에 정렬된 형식 멤버와 함께 사용될 수 있습니다.

## Methods
An expression-bodied method consists of a single expression that returns a value whose type matches the method's return type, or, for methods that return void, that performs some operation. For example, types that override the ToString method typically include a single expression that returns the string representation of the current object.

식 본문 메서드는 메서드의 반환 형식과 일치하는 값을 반환하거나, void를 반환하는 메서드에 대해 어떤 연산을 수행하는 단일 식으로 구성됩니다. 예를 들어, ToString 메서드를 재정의 하는 형식은 보통 현재 객체의 문자열 표현을 반환하는 단일 식을 포함합니다.

The following example defines a Person class that overrides the ToString method with an expression body definition. It also defines a Show method that displays a name to the console. Note that the return keyword is not used in the ToString expression body definition.

다음 예제는 식 본문 정의로 ToString 메서드를 재정의하는 Person 클래스를 정의합니다. console에 name을 보여주는 Show 메서드를 정의합니다. return 키워드는 ToString 식 본문 정의에서 사용되지 않습니다.
~~~
using System;

public class Person
{
    public Person(string firstName, string lastName)
    {
        fname = firstName;
        lname = lastName;
    }

    private string fname;
    private string lname;

    public override string ToString() => $"{fname} {lname}".Trim();
    public void DisplayName() => Console.WriteLine(ToString());
}

class Example
{
    static void Main()
   {
      Person p = new Person("Mandy", "Dejesus");
      Console.WriteLine(p);
      p.DisplayName();
   }
}
~~~

## Constructors
An expression body definition for a constructor typically consists of a single assignment expression or a method call that handles the constructor's arguments or initializes instance state.

생성자에 대한 식 본문 정의는 보통 단일 할당 식 또는 생성자의 인수 또는 초기화 인스턴스 상태를 처리하는 메서드 호출로 구성됩니다.

The following example defines a Location class whose constructor has a single string parameter named name. The expression body definition assigns the argument to the Name property.

다음 예제는 생성자가 name이라는 단일 문자열 매개변수가 있는 Location 클래스를 정의합니다. 식 본문 정의는 Name 속성에 인수를 할당합니다.
~~~
public class Location
{
    private string locationName;

    public Location(string name) => locationName = name;

    public string Name
    {
        get => locationName;
        set => locationName = value;
    }
}
~~~
## Finalizers
An expression body definition for a finalizer typically contains cleanup statements, such as statements that release unmanaged resources.

소멸자에 대한 식 본문 정의는 보통 관리되지 않는 리소스를 해체하는 문장같은 청소하는 문장을 포함합니다.

The following example defines a finalizer that uses an expression body definition to indicate that the finalizer has been called.

다음 예제는 소멸자가 호출되었음을 나타내기 위해 식 본문 정의를 사용하는 호출자를 정의합니다.
~~~
using System;

public class Destroyer
{
    public override string ToString() => GetType().Name;

    ~Destroyer() => Console.WriteLine($"The {ToString()} destructor is executing.");
}
~~~