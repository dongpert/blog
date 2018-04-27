---
title: static_class_and_static_class_members
tags:
---
A static class is basically the same as a non-static class, but there is one difference: a static class cannot be instantiated. In other words, you cannot use the new keyword to create a variable of the class type. Because there is no instance variable, you access the members of a static class by using the class name itself. For example, if you have a static class that is named UtilityClass that has a public method named MethodA, you call the method as shown in the following example:

정적 멤버는 기본적으로 비 정적 클래스와 같습니다. 그러나 한가지 다른점이 있습니다 정적 메서드는 인스턴스화 될 수 없습니다. 즉 클래스 형식의 변수를 생성하기 위해 새로운 키워드를 사용할 수 없습니다. 인스턴스 변수가 없기 때문에 클래스 자신의 이름을 사용하여 정적클래스의 멤버에 접근합니다. 예를들어 MethodA 이름의 공용 메서드가 있는 UtilityClass 이름의 정적클래스가 있다면 다음 예제에서 보여주듯이 메서드를 호출할 수 있습니다.

~~~
UtilityClass.MethodA(); 
~~~

A static class can be used as a convenient container for sets of methods that just operate on input parameters and do not have to get or set any internal instance fields. For example, in the .NET Framework Class Library, the static System.Math class contains methods that perform mathematical operations, without any requirement to store or retrieve data that is unique to a particular instance of the Math class. That is, you apply the members of the class by specifying the class name and the method name, as shown in the following example.

정적클래스는 입력 매개변수에 대해서만 작동하고 내부 인스턴스 필드를 가져오거나 설정할 필요가 없는 메서드 집합에 대한 편리한 컨테이너로 사용될 수 있습니다. 예를들어 닷넷 프레임워크 클래스 라이브러리에서 정적 System.Math 클래스는 Math 클래스의 특정한 인스턴스에 고유한 데이터를 저장하거나 검색하는데 필요조건 없이 수학적 연산을 수행하는 메서드를 포함합니다. 즉 클래스의 이름과 메서드의 이름을 지정하여 클래스의 멤버를 적용할 수 있습니다.

~~~
double dub = -3.14;  
Console.WriteLine(Math.Abs(dub));  
Console.WriteLine(Math.Floor(dub));  
Console.WriteLine(Math.Round(Math.Abs(dub)));  

// Output:  
// 3.14  
// -4  
// 3  
~~~

As is the case with all class types, the type information for a static class is loaded by the .NET Framework common language runtime (CLR) when the program that references the class is loaded. The program cannot specify exactly when the class is loaded. However, it is guaranteed to be loaded and to have its fields initialized and its static constructor called before the class is referenced for the first time in your program. A static constructor is only called one time, and a static class remains in memory for the lifetime of the application domain in which your program resides.

모든 클래스 형식과 마찬가지로 정적클래스에 대한 형식 정보는 클래스를 참조하는 프로그램이 로드될때 닷넷 프레임워크 CLR에 의해 로드됩니다. 프로그램은 클래스가 로드되는 시기를 정확하게 지정할 수 없습니다. 그러나 클래스가 프로그램에서 최초로 참조 되기전에 참조되기 전에 로드되고 필드 초기화되고 정적생성자가 호출되는것을 보장합니다. 정적생성자는 한번만 호출되고 정적 클래스는 프로그램이 상주하는 어플 도메인의 수명 동안 메모리에 남아있습니다.

> Note
>
> To create a non-static class that allows only one instance of itself to be created, see Implementing Singleton in C#.
> 자신의 인스턴스 한나만 생성되도록 하는 비정적 클래스를 생성하기 위해서는 C#에서 싱글톤 구현을 보십시요.

The following list provides the main features of a static class:
다음 목록은 정적 클래스의 주요한 특징들을 제공합니다.

* Contains only static members.
* Cannot be instantiated.
* Is sealed.
* Cannot contain Instance Constructors.

* 오직 정적 멤버만을 포함합니다.
* 인스턴스화 될 수 없습니다.
* 봉인되어 있습니다.
* 인스턴스 생성자를 포함 할 수 없습니다.

Creating a static class is therefore basically the same as creating a class that contains only static members and a private constructor. A private constructor prevents the class from being instantiated. The advantage of using a static class is that the compiler can check to make sure that no instance members are accidentally added. The compiler will guarantee that instances of this class cannot be created.

정적클래스를 생성하는 것은 기본적으로 정적 멤버와 private 생성자만을 포함하는 클래스를 생성하는것과 동일합니다. private 생성자는 인스턴스화 되지 않도록 합니다. 정적 클래스를 사용의 장점은 컴파일러가 인스턴스 멤버가 잘못하여 추가되는 않았는지 확인할 수 있다는것 입니다. 컴파일러는 이 클래스의 인스턴스는 생성될 수 없다는 것을 보장할 것입니다.

Static classes are sealed and therefore cannot be inherited. They cannot inherit from any class except Object. Static classes cannot contain an instance constructor; however, they can contain a static constructor. Non-static classes should also define a static constructor if the class contains static members that require non-trivial initialization. For more information, see [Static Constructors](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-constructors).

정적클래스는 봉인되어 상속될 수 없습니다. 객체를 제외한 어떤클래스에 상속할 수 없습니다. 정적클래스는 인스턴스 생성자를 포함할 수 없습니다. 그러나 정적생성자를 포함할 수 있습니다.  클래스가 사소한 초기화가 필요한 정적멤버를 포함한다면 비정적클래스는 정적 생성자를 정의해야 합니다.

## Example
Here is an example of a static class that contains two methods that convert temperature from Celsius to Fahrenheit and from Fahrenheit to Celsius:
여기 온도를 화씨에서 섭씨로 섭씨에서 화씨로 변환하는 두개의 메서드를 포함하는 정적 클래스 예제가 있습니다.

~~~
public static class TemperatureConverter
{
    public static double CelsiusToFahrenheit(string temperatureCelsius)
    {
        // Convert argument to double for calculations.
        double celsius = Double.Parse(temperatureCelsius);

        // Convert Celsius to Fahrenheit.
        double fahrenheit = (celsius * 9 / 5) + 32;

        return fahrenheit;
    }

    public static double FahrenheitToCelsius(string temperatureFahrenheit)
    {
        // Convert argument to double for calculations.
        double fahrenheit = Double.Parse(temperatureFahrenheit);

        // Convert Fahrenheit to Celsius.
        double celsius = (fahrenheit - 32) * 5 / 9;

        return celsius;
    }
}

class TestTemperatureConverter
{
    static void Main()
    {
        Console.WriteLine("Please select the convertor direction");
        Console.WriteLine("1. From Celsius to Fahrenheit.");
        Console.WriteLine("2. From Fahrenheit to Celsius.");
        Console.Write(":");

        string selection = Console.ReadLine();
        double F, C = 0;

        switch (selection)
        {
            case "1":
                Console.Write("Please enter the Celsius temperature: ");
                F = TemperatureConverter.CelsiusToFahrenheit(Console.ReadLine());
                Console.WriteLine("Temperature in Fahrenheit: {0:F2}", F);
                break;

            case "2":
                Console.Write("Please enter the Fahrenheit temperature: ");
                C = TemperatureConverter.FahrenheitToCelsius(Console.ReadLine());
                Console.WriteLine("Temperature in Celsius: {0:F2}", C);
                break;

            default:
                Console.WriteLine("Please select a convertor.");
                break;
        }

        // Keep the console window open in debug mode.
        Console.WriteLine("Press any key to exit.");
        Console.ReadKey();
    }
}
/* Example Output:
    Please select the convertor direction
    1. From Celsius to Fahrenheit.
    2. From Fahrenheit to Celsius.
    :2
    Please enter the Fahrenheit temperature: 20
    Temperature in Celsius: -6.67
    Press any key to exit.
 */
~~~

## Static Members
A non-static class can contain static methods, fields, properties, or events. The static member is callable on a class even when no instance of the class has been created. The static member is always accessed by the class name, not the instance name. Only one copy of a static member exists, regardless of how many instances of the class are created. Static methods and properties cannot access non-static fields and events in their containing type, and they cannot access an instance variable of any object unless it is explicitly passed in a method parameter.

It is more typical to declare a non-static class with some static members, than to declare an entire class as static. Two common uses of static fields are to keep a count of the number of objects that have been instantiated, or to store a value that must be shared among all instances.

Static methods can be overloaded but not overridden, because they belong to the class, and not to any instance of the class.

Although a field cannot be declared as static const, a const field is essentially static in its behavior. It belongs to the type, not to instances of the type. Therefore, const fields can be accessed by using the same ClassName.MemberName notation that is used for static fields. No object instance is required.

C# does not support static local variables (variables that are declared in method scope).

You declare static class members by using 
the static keyword before the return type of the member, as shown in the following example:

~~~
public class Automobile
{
    public static int NumberOfWheels = 4;
    public static int SizeOfGasTank
    {
        get
        {
            return 15;
        }
    }
    public static void Drive() { }
    public static event EventType RunOutOfGas;

    // Other non-static fields and properties...
}
~~~

Static members are initialized before the static member is accessed for the first time and before the static constructor, if there is one, is called. To access a static class member, use the name of the class instead of a variable name to specify the location of the member, as shown in the following example:

~~~
Automobile.Drive();
int i = Automobile.NumberOfWheels;
~~~

If your class contains static fields, provide a static constructor that initializes them when the class is loaded.

A call to a static method generates a call instruction in Microsoft intermediate language (MSIL), whereas a call to an instance method generates a callvirt instruction, which also checks for a null object references. However, most of the time the performance difference between the two is not significant.