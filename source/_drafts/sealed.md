---
title: sealed (C# Reference)
tags:
---
When applied to a class, the sealed modifier prevents other classes from inheriting from it. In the following example, class B inherits from class A, but no class can inherit from class B.

클래스에 적용될 때 sealed 한정자는 다른 클래스가 클래스에서 상속하는 것을 방지합니다. 클래스 B는 클래스 A를 상속합니다. 그러나 B클래스를 상속할 수 있는 클래스는 없습니다.

~~~
class A {}      
sealed class B : A {}  
~~~

You can also use the sealed modifier on a method or property that overrides a virtual method or property in a base class. This enables you to allow classes to derive from your class and prevent them from overriding specific virtual methods or properties.

기본클래스에 메서드나 가상 메서드나 속성을 재정의하는 속성에서 sealed 한정자를 사용할 수 있습니다. 클래스에서 클래스를 파생시키고 특정 가상 메서드나 속성을 재정의하지 못하게 한다.

### Example
~~~
class X
{
    protected virtual void F() { Console.WriteLine("X.F"); }
    protected virtual void F2() { Console.WriteLine("X.F2"); }
}
class Y : X
{
    sealed protected override void F() { Console.WriteLine("Y.F"); }
    protected override void F2() { Console.WriteLine("Y.F2"); }
}
class Z : Y
{
    // Attempting to override F causes compiler error CS0239.
    // protected override void F() { Console.WriteLine("C.F"); }

    // Overriding F2 is allowed.
    protected override void F2() { Console.WriteLine("Z.F2"); }
}
~~~

When you define new methods or properties in a class, you can prevent deriving classes from overriding them by not declaring them as virtual.

새로운 메서드나 속성들을 클래스에 정의하면 클래스를 가상으로 선언하지 않아 클래스가 파생되는것을 막을 수 있습니다.

It is an error to use the abstract modifier with a sealed class, because an abstract class must be inherited by a class that provides an implementation of the abstract methods or properties.

sealed 클래스에서 abstract 한정자를 사용하는 것은 오류를 발생시킵니다. 추상클래스는 추상 메서드나 속성의 구현을 제공하는 클래스에 상속되어야만 하기 때문입니다.

When applied to a method or property, the sealed modifier must always be used with override.
메서드나 속성에 적용되면 sealed 한정자는 항상 override와 함께 사용되야 합니다.

Because structs are implicitly sealed, they cannot be inherited.
구조체는 암시적으로 봉인되기 때문에 상속될 수 없습니다.

For more information, see Inheritance.

For more examples, see Abstract and Sealed Classes and Class Members.

### Example
~~~
sealed class SealedClass
{
    public int x;
    public int y;
}

class SealedTest2
{
    static void Main()
    {
        SealedClass sc = new SealedClass();
        sc.x = 110;
        sc.y = 150;
        Console.WriteLine("x = {0}, y = {1}", sc.x, sc.y);
    }
}
// Output: x = 110, y = 150
~~~