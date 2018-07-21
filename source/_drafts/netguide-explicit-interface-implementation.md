---
title: netguide-explicit-interface-implementation
tags:
---
If a class implements two interfaces that contain a member with the same signature, then implementing that member on the class will cause both interfaces to use that member as their implementation. In the following example, all the calls to Paint invoke the same method.

클래스가 같은 시그니처의 멤버를 포함하는 두개의 인터페이스를 구현한다는 경우 클래스에 멤버를 구현하면 두 인터페이스는 그들의 구현처럼 멤버를 사용하게 됩니다. 다음 예제에서는 Paint를 호출하는 것은 같은 메서드를 호출합니다.
~~~
class Test
{
    static void Main()
    {
        SampleClass sc = new SampleClass();
        IControl ctrl = (IControl)sc;
        ISurface srfc = (ISurface)sc;

        // The following lines all call the same method.
        sc.Paint();
        ctrl.Paint();
        srfc.Paint();
    }
}

interface IControl
{
    void Paint();
}
interface ISurface
{
    void Paint();
}
class SampleClass : IControl, ISurface
{
    // Both ISurface.Paint and IControl.Paint call this method. 
    public void Paint()
    {
        Console.WriteLine("Paint method in SampleClass");
    }
}
~~~
If the two interface members do not perform the same function, however, this can lead to an incorrect implementation of one or both of the interfaces. It is possible to implement an interface member explicitly—creating a class member that is only called through the interface, and is specific to that interface. This is accomplished by naming the class member with the name of the interface and a period. For example:

두개의 인터페이스 메서드가 같은 함수를 수행하지 않는다면 인터페이스 한개 혹은 각각의 잘못된 구현을 초래할 수 있습니다. 인터페이스 멤버를 명시적 생성으로 인터페이스를 통해서만 호출되고 해당 인터페이스에 특정된 클래스 멤버로 구현할 수 있습니다. 이것은 인터페이스의 이름과 마침표와 함께 클래스 멤버의 이름을 지정하여 완성됩니다.
~~~
public class SampleClass : IControl, ISurface
{
    void IControl.Paint()
    {
        System.Console.WriteLine("IControl.Paint");
    }
    void ISurface.Paint()
    {
        System.Console.WriteLine("ISurface.Paint");
    }
}
~~~
The class member IControl.Paint is only available through the IControl interface, and ISurface.Paint is only available through ISurface. Both method implementations are separate, and neither is available directly on the class. For example:

IControl.Paint는 IControl 인터페이스를 통해서만 이용할 수 있고 ISurface.Paint는 오직 ISurface를 통해서만 이용할 수 있습니다. 두 메서드 구현은 분리되어있고 둘다 클래스에서 직접 이용되 수 없습니다.
~~~
// Call the Paint methods from Main.

SampleClass obj = new SampleClass();
//obj.Paint();  // Compiler error.

IControl c = (IControl)obj;
c.Paint();  // Calls IControl.Paint on SampleClass.

ISurface s = (ISurface)obj;
s.Paint(); // Calls ISurface.Paint on SampleClass.

// Output:
// IControl.Paint
// ISurface.Paint
~~~
Explicit implementation is also used to resolve cases where two interfaces each declare different members of the same name such as a property and a method:

명시적 구현은 두개의 인터페이스가 각각 속성과 메서드와 처럼 같은 이름의 다른 멤버로 선언되는 경우를 해결하기 위해 사용됩니다.
~~~
interface ILeft
{
    int P { get;}
}
interface IRight
{
    int P();
}
~~~
To implement both interfaces, a class has to use explicit implementation either for the property P, or the method P, or both, to avoid a compiler error. For example:

각 인터페이스를 구현하기 위해서 클래스는 속성 P, 또는 메서드 P 또는 둘다에 대해서 컴파일러 오류를 피하기 위해서 명시적 선언을 사용해야 합니다.
~~~
class Middle : ILeft, IRight
{
    public int P() { return 0; }
    int ILeft.P { get { return 0; } }
}
~~~