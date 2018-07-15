---
title: netguide-abstract-and-sealed-classes-and-class-members
tags:
---
The abstract keyword enables you to create classes and class members that are incomplete and must be implemented in a derived class.

abstract 키워드는 불완전하고 파생 클래스에서 구현되어야만 하는 클래스와 클래스 멤버를 생성할 수 있게 합니다.

The sealed keyword enables you to prevent the inheritance of a class or certain class members that were previously marked virtual.

sealed 키워드는 클래스나 이전에 virtual로 표시된 특정 클래스 멤버의 상속을 방지할 수 있게 합니다.

## Abstract Classes and Class Members
Classes can be declared as abstract by putting the keyword abstract before the class definition. For example:

클래스는 클래스 정의 앞에 abstract 키워드를 넣어 추상으로 선언될 수 있습니다.
~~~
public abstract class A
{
    // Class members here.
}
~~~
An abstract class cannot be instantiated. The purpose of an abstract class is to provide a common definition of a base class that multiple derived classes can share. For example, a class library may define an abstract class that is used as a parameter to many of its functions, and require programmers using that library to provide their own implementation of the class by creating a derived class.

추상 클래스는 인스턴스화 될 수 없습니다. 추상 클래스의 목적은  여러 파생된 클래스가 공유 할 수 있는 기본 클래스의 공통 정의를 제공하기 위해서 입니다. 예를 들어 클래스 라이브러리는 많은 함수에 매개변수로 사용되는 추상클래스를 정의할 수 있고 라이브러리를 사용하는 프로그래머들이 그들의 클래스의 구현을 제공하는 것이 필요합니다.

Abstract classes may also define abstract methods. This is accomplished by adding the keyword abstract before the return type of the method. For example:

추상 클래스는 추상 메서드를 정의할 수 있습니다. 메서드의 반환 타입 앞에 abstract 키워드를 추가하여 정의됩니다.
~~~
public abstract class A
{
    public abstract void DoWork(int i);
}
~~~
Abstract methods have no implementation, so the method definition is followed by a semicolon instead of a normal method block. Derived classes of the abstract class must implement all abstract methods. When an abstract class inherits a virtual method from a base class, the abstract class can override the virtual method with an abstract method. For example:

추상 메서드는 구현이 없습니다. 그래서 메서드 정의는 보통 메서드 블록 대신에 세미콜론이 따라옵니다. 추상 클래스의 파생된 클래스는 모든 추상 메서드를 구현해야만 합니다. 추상 클래스가 기본 클래스에 가상 메서드를 상속하면, 추상클래스는 추상 메서드로 가상 메서드를 재정의 할 수 있습니다.
~~~
// compile with: -target:library
public class D
{
    public virtual void DoWork(int i)
    {
        // Original implementation.
    }
}

public abstract class E : D
{
    public abstract override void DoWork(int i);
}

public class F : E
{
    public override void DoWork(int i)
    {
        // New implementation.
    }
}
~~~
If a virtual method is declared abstract, it is still virtual to any class inheriting from the abstract class. A class inheriting an abstract method cannot access the original implementation of the method—in the previous example, DoWork on class F cannot call DoWork on class D. In this way, an abstract class can force derived classes to provide new method implementations for virtual methods.

가상 메서드는 추상으로 선언되면 추상 클래스에서 상속되는 모든 클래스는 여전히 가상입니다. 추상 클래스를 상속하는 클래스는 메서드의 원본 구현에 접근할 수 없습니다. 이전 예제에서 F 클래스에 DoWork는 D 클래스에 DoWork를 호출할 수 없습니다. 이 방식으로 추상 클래스는 파생 클래스가 가상 메서드에 대해 새로운 메서드 구현을 제공하는 것을 강제할 수 있습니다.