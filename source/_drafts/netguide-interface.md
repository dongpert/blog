---
title: netguide-interface
tags:
---
An interface contains definitions for a group of related functionalities that a class or a struct can implement.

인터페이스는 클래스 또는 구조체가 구현할 수 있는 기능들과 관련된 그룹에 대한 정의를 포함합니다.

By using interfaces, you can, for example, include behavior from multiple sources in a class. That capability is important in C# because the language doesn't support multiple inheritance of classes. In addition, you must use an interface if you want to simulate inheritance for structs, because they can't actually inherit from another struct or class.

인터페이스를 사용하면 예를들어 당신은 클래스에 여러개의 소스에 동작을 포함시킬 수 있습니다. 언어는 클래스의 다중 상속을 지원하지 않기 때문에 이 능력은 C#에서 중요합니다. 게다가 다른 구조체나 클래스에서 실제로 상속할 수 없기 때문에 구조체에 대한 상속을 시뮬레이션 하려면 인터페이스를 사용해야 합니다.

You define an interface by using the interface keyword, as the following example shows.

interface 키웓를 사용하여 인터페이스를 정의해야 합니다.
~~~
interface IEquatable<T>
{
    bool Equals(T obj);
}
~~~
Any class or struct that implements the IEquatable<T> interface must contain a definition for an Equals method that matches the signature that the interface specifies. As a result, you can count on a class that implements IEquatable<T> to contain an Equals method with which an instance of the class can determine whether it's equal to another instance of the same class.

IEquatable\<T\> 인터페이스를 구현하는 어떤 클래스 또는 구조체는 인터페이스가 명시하는 시그니처와 일치하는 Equals 메서드에 대한 구현을 포함해야만 합니다. 결과적으로 클래스의 인스턴스가 같은 클래스의 다른 인스터스와 같은지 여부를 확인할 수 있는 Equals 메서드를 포함시키기 위해 IEquatable\<T\>를 구현하는 클래스에 의지할 수 있습니다.

The definition of IEquatable<T> doesn’t provide an implementation for Equals. The interface defines only the signature. In that way, an interface in C# is similar to an abstract class in which all the methods are abstract. However, a class or struct can implement multiple interfaces, but a class can inherit only a single class, abstract or not. Therefore, by using interfaces, you can include behavior from multiple sources in a class.

IEquatable\<T\>의 정의는 Equals에 대한 구현을 제공하지 않습니다. 인터페이스는 시그니처만 정의합니다. 이런식으로 C#에 인터페이스는 모든 메서드가 추상인 추상클래스와 비슷합니다. 그러나 클래스 또는 구조체는 다중 인터페이스를 구현할 수 있습니다. 그러나 클래스는 추상이거나 아닌 단일 클래스만 상속할 수 있습니다. 그러므로 인터페이스를 사용하면 클래스에 여러 소스에 동작들을 포함시킬 수 있습니다.

For more information about abstract classes, see Abstract and Sealed Classes and Class Members.

Interfaces can contain methods, properties, events, indexers, or any combination of those four member types. For links to examples, see Related Sections. An interface can't contain constants, fields, operators, instance constructors, finalizers, or types. Interface members are automatically public, and they can't include any access modifiers. Members also can't be static.

인터페이스는 메서드, 속성, 이벤트, 인덱서, 또는 이러한 4개 멤버 형식의 어떤 조합이라도 포함할 수 있습니다. 인터페이스는 상수, 필드, 연산자, 인스턴스 생성자, 소멸자, 또는 형식을 포함할 수 없습니다. 인터페이스 멤버는 자동적으로 공용입니다. 그리고 어떤 접근 한정자를 포함할 수 없습니다. 멤버는 정적일 수 없습니다.

To implement an interface member, the corresponding member of the implementing class must be public, non-static, and have the same name and signature as the interface member.

인터페이스 멤버를 구현하기 위해 구현하는 클래스의 해당하는 멤버는 공용, 비정적, 그리고 인터페이스와 동일한 이름 및 시그니처를 가져야 합니다. 

When a class or struct implements an interface, the class or struct must provide an implementation for all of the members that the interface defines. The interface itself provides no functionality that a class or struct can inherit in the way that it can inherit base class functionality. However, if a base class implements an interface, any class that's derived from the base class inherits that implementation.

클래스 또는 구조체가 인터페이스를 구현할 때 클래스 또는 구조체는 인터페이스가 정의하는 멤버의 모든것에 대한 구현을 제공해야만 합니다. 인터페이스 자신은 클래스 또는 구조체가 기본 클래스 기능을 상속할 수 있는 방식으로 상속할 수 있는 기능을 제공할 수 없습니다. 그러나 기본 클래스가 인터페이스를 구현한다면 기본 클래스에서 파생된 모든 클래스는 구현을 상속합니다.

The following example shows an implementation of the IEquatable<T> interface. The implementing class, Car, must provide an implementation of the Equals method.
~~~
public class Car : IEquatable<Car>
{
    public string Make {get; set;}
    public string Model { get; set; }
    public string Year { get; set; }

    // Implementation of IEquatable<T> interface
    public bool Equals(Car car)
    {
        if (this.Make == car.Make &&
            this.Model == car.Model &&
            this.Year == car.Year)
        {
            return true;
        }
        else
            return false;
    }
}
~~~
Properties and indexers of a class can define extra accessors for a property or indexer that's defined in an interface. For example, an interface might declare a property that has a get accessor. The class that implements the interface can declare the same property with both a get and set accessor. However, if the property or indexer uses explicit implementation, the accessors must match. For more information about explicit implementation, see Explicit Interface Implementation and Interface Properties.

클래스의 속성과 인덱서는 인터페이스에 정의되어 있는 속성이나 인덱서에 대한 추가 접근자를 정의할 수 있습니다. 예를 들어 인터페이스가 get 접근자를 가지는 속성을 선언할 수 있습니다. 인터페이스를 구현하는 클래스는 set과 get 접근자가 있는 같은 속성을 선언할 수 있습니다. 그러나 속성이나 인덱서는 명시적 구현을 사용한다면 접근자는 일치해야 합니다.

Interfaces can implement other interfaces. A class might include an interface multiple times through base classes that it inherits or through interfaces that other interfaces implement. However, the class can provide an implementation of an interface only one time and only if the class declares the interface as part of the definition of the class (class ClassName : InterfaceName). If the interface is inherited because you inherited a base class that implements the interface, the base class provides the implementation of the members of the interface. However, the derived class can reimplement the interface members instead of using the inherited implementation.

인터페이스는 다른 인터페이스를 구현할 수 있습니다 클래스는 상속하는 기본클래스나 다른 인터페이스를 구현하는 인터페이스를 통하여 여러번 인터페이스를 포함할 수 있습니다. 그러나 클래스는 인터페이스의 구현을 한번만 제공할 수 있고 클래스가 클래스의 정의의 부분으로 인터페이스를 선언하면 제공할 수 있습니다. 인터페이스를 구현하는 기본 클래스를 상속했기 때문에 인터페이스가 상속되면 기본 클래스는 인터페이스의 멤버의 구현을 제공합니다. 그러나 파생된 클래스는 상속된 구현을 사용하는 대신에 인터페이스 멤버를 재구현할 수 있습니다.

A base class can also implement interface members by using virtual members. In that case, a derived class can change the interface behavior by overriding the virtual members. For more information about virtual members, see Polymorphism.

기본 클래스는 가상 멤버를 사용하여 인터페이스 멤버를 구현할 수 있습니다. 이 경우 파생된 클래스는 가상 멤버를 재정의하여 인터페이스 동작을 변경할 수 있습니다.

## Interfaces Summary
An interface has the following properties:

* An interface is like an abstract base class. Any class or struct that implements the interface must implement all its members.

* An interface can't be instantiated directly. Its members are implemented by any class or struct that implements the interface.

* Interfaces can contain events, indexers, methods, and properties.

* Interfaces contain no implementation of methods.

* A class or struct can implement multiple interfaces. A class can inherit a base class and also implement one or more interfaces.