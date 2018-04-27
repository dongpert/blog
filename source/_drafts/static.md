---
title: static
tags:
---
Use the static modifier to declare a static member, which belongs to the type itself rather than to a specific object. The static modifier can be used with classes, fields, methods, properties, operators, events, and constructors, but it cannot be used with indexers, finalizers, or types other than classes. For more information, see [Static Classes and Static Class Members](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members).

특정 객체가 아닌 형식 자체에 속하는 정적 멤버를 선언하기 위해 정적 한정자를 사용합니다. 정적 멤버는 클래스, 필드, 메섣, 속성, 연산자, 이벤트 그리고 구조체와 함께 사용될 수 있습니다. 그러나 인덱서, 종료자 또는 클래스 이외의 형식과 함께 사용할 수 없습니다.

## Example
The following class is declared as static and contains only static methods:
다음 클래스는 정적으로 선언되었고 정적 메서드만을 포함합니다.

~~~
static class CompanyEmployee
{
    public static void DoSomething() { /*...*/ }
    public static void DoSomethingElse() { /*...*/  }
}
~~~

A constant or type declaration is implicitly a static member.

상수 또는 형식 선언은 암시적인 정적 멤버입니다.

A static member cannot be referenced through an instance. Instead, it is referenced through the type name. For example, consider the following class

정적 멤버는 인스턴스를 통하여 참조될 수 없습니다. 대신에 형식 이름으로 참조됩니다. 예를들어 다음 클래스를 생각해봅시다.

~~~
public class MyBaseC
{
    public struct MyStruct
    {
        public static int x = 100;
    }
}
~~~

To refer to the static member x, use the fully qualified name, MyBaseC.MyStruct.x, unless the member is accessible from the same scope:

같은 범위에서 접근할 수 있는한 정적 멤버 x를 참조하기 위해 완전히 정규화된 이름 MyBaseC.MyStruct.x을 사용합니다.

~~~
Console.WriteLine(MyBaseC.MyStruct.x);  
~~~

While an instance of a class contains a separate copy of all instance fields of the class, there is only one copy of each static field.

클래스의 인스턴스는 클래스의 모든 인스턴스 필드의 별도의 복사본을 포함하지만 각 정적 필드의 복사본은 한개만 있습니다.

It is not possible to use this to reference static methods or property accessors.

정적 메서드 속성 접근자를 참조하기 위해 this를 사용할 수 없습니다.

If the static keyword is applied to a class, all the members of the class must be static.

정적 키워드가 클래스에 적용되면 클래스의 모든 메버는 정적이여야 합니다.

Classes and static classes may have static constructors. Static constructors are called at some point between when the program starts and the class is instantiated.

클래스와 정적 멤버는 정적 생성자가 있을 수 있습니다. 정적 생성자는 프로그램이 시작하고 클래스가 인스턴스화 되는 사이 어느 시점에 호출됩니다.

> Note
>
> The static keyword has more limited uses than in C++. To compare with the C++ keyword, see Storage classes (C++).
> 정적 키워드는 C++에서 보다 사용제 제한적입니다.

To demonstrate static members, consider a class that represents a company employee. Assume that the class contains a method to count employees and a field to store the number of employees. Both the method and the field do not belong to any instance employee. Instead they belong to the company class. Therefore, they should be declared as static members of the class.

정적 멤버를 설명하기 위해 회사 직원을 표현하는 클래스를 생각하십시요. 직원을 세는 메서드와 직원의 수를 저장하기 위한 필드를 포함하는 클래스를 가정하십시요. 메서드와 필드는 어떤 인스턴스 직원에 속하지 않습니다. 대신에 회사 클래스에 속합니다. 그러므로 클래스의 정적 멤버로 선언되어야 합니다.

## Example
This example reads the name and ID of a new employee, increments the employee counter by one, and displays the information for the new employee and the new number of employees. For simplicity, this program reads the current number of employees from the keyboard. In a real application, this information should be read from a file.

예제는 새로운 직원의 ID와 이름을 읽고 하나씩 직원 카운터를 증가시키고 새로운 직원과 직원의 새로운 번호에 대한 정보를 보여줍니다. 간단하게 하기위해 프로그램은 키보드에서 현재 직원의 수를 읽습니다. 실제 어플에서 이정보는 파일에서 읽어야 합니다.

~~~
public class Employee4
{
    public string id;
    public string name;

    public Employee4()
    {
    }

    public Employee4(string name, string id)
    {
        this.name = name;
        this.id = id;
    }

    public static int employeeCounter;

    public static int AddEmployee()
    {
        return ++employeeCounter;
    }
}

class MainClass : Employee4
{
    static void Main()
    {
        Console.Write("Enter the employee's name: ");
        string name = Console.ReadLine();
        Console.Write("Enter the employee's ID: ");
        string id = Console.ReadLine();

        // Create and configure the employee object:
        Employee4 e = new Employee4(name, id);
        Console.Write("Enter the current number of employees: ");
        string n = Console.ReadLine();
        Employee4.employeeCounter = Int32.Parse(n);
        Employee4.AddEmployee();

        // Display the new information:
        Console.WriteLine("Name: {0}", e.name);
        Console.WriteLine("ID:   {0}", e.id);
        Console.WriteLine("New Number of Employees: {0}",
                      Employee4.employeeCounter);
    }
}
/*
    Input:
    Matthias Berndt
    AF643G
    15
     * 
    Sample Output:
    Enter the employee's name: Matthias Berndt
    Enter the employee's ID: AF643G
    Enter the current number of employees: 15
    Name: Matthias Berndt
    ID:   AF643G
    New Number of Employees: 16
*/
~~~

##Example
This example shows that although you can initialize a static field by using another static field not yet declared, the results will be undefined until you explicitly assign a value to the static field.

이 예제는 아직 선언되지 않은 다른 정적 필드를 사용하여 정적 필드를 초기화 할 수 있지만 명시적으로 값을 정적필드에 할당할 때까지 결과는 undefined가 될 것이라는 걸 보여줍니다.

~~~
class Test
{
   static int x = y;
   static int y = 5;

   static void Main()
   {
      Console.WriteLine(Test.x);
      Console.WriteLine(Test.y);

      Test.x = 99;
      Console.WriteLine(Test.x);
   }
}
/*
Output:
    0
    5
    99
*/
~~~