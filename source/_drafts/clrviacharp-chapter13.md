---
title: clrviacharp-chapter13
tags:
---
### 클래스와 인터페이스 상속
Object의 메서드들은 마이크로소프트사의 누군가 구현하였기 때문에 이를 상속한 클래스들은 실제로 다음의 내용들을 상속하는 것이다.
* 메서드 원형: Object 클래스의 인스턴스를 사용하는 것처럼 코드를 작성하더라도, 실제로는 다른 클래스의 인스턴스를 동일하게 사용하는 것이다.
* 메서드에 대한 구현부: 개발자들이 Object 클래스를 상속한 새로운 클래스를 정의할 때, Object 메서드가 정의하고 있는 메서드를 따로 구현해야 할 필요가 없다.

### 인터페이스 정의하기
인터페이스는 메서드 원형들의 집합에 이름을 붙인 것이다. 인터페이스에는 메서드 외에 이벤트, 매개변수 없는 속성, C#의 인덱서와 같이 매개변수 받는 속성 등이 포함될수도 있는데, 결국 메서드를 지칭하기 위한 구문상 편의사항이기 때문이다. 그러나 인터페이스는 생성자 메서드를 정의할 수 없으며 인스턴스 필드들도 정의할 수 없다.

### 인터페이스 상속하기
~~~
public interface IComparable<in T> {
    Int32 CompareTo(T other);
}

public sealed class Point : IComparable<Point> {
    private Int32 m_x, m_y;

    public Point(Int32 x, Int32 y) {
        m_x = x;
        m_y = y;
    }

    public Int32 CompareTo(Point other) {
        return Math.Sign(Math.Sqrt(m_x * m_x + m_y * m_y) - Math.Sqrt(other.m_x * other.m_x + other.m_y * other.m_y));
    }

    public override String ToString() {
        return String.Format("({0}, {1})", m_x, m_y);
    }
}

public static class Program {
    public static void Main() {
        Point[] points = new Point[] {
            new Point(3, 3),
            new Point(1, 2)
        };

        if (points[0].COmpareTo(points[1]) > 0) {
            Point tempPoint = points[0];
            points[0] = points[1];
            points[1] = tempPoint;
        }
        Console.WriteLine("Points from closest to (0, 0) to farthest:");
        foreach (Point p in points) {
            Console.WriteLine(p);
        }
    }
}
~~~
C# 컴파일러는 인터페이스 메서드를 구현할 때 public으로 선언하도록 요구한다. CLR은 인터페이스 메서드가 virtual로 정의할 것을 요구한다. 만약 소스 코드에서 메서드를 구현할 때 명시적으로 virtual 키워드를 사용하지 않으면 컴파일러가 해당 메서드에 virtual 키워드와 sealed 키워드를 포함시키며, 이 경우 이 클래스를 상속한 다른 클래스에서 인터페이스 메서드를 재정의 할 수 없다. 만약 명시적으로 메서드에 virtual 키워드를 지정하면 해당 클래스를 상속한 다른 클래스에서 인터페이스 메서드를 재정의할 수 있다.

인터페이스 메서드를 구현할 때 sealed가 포함된 경우, 이 클래스를 상속한 클래스에서는 메서드를 재정의할 수 없다고 하였다. 하지만 이 클래스를 상속한 다른 클래스는 또 다시 동일 인터페이스를 상속받아 구현함으로써 자체적으로 새로운 메서드를 구현할 수는 있다. 이 경우 해당 객체에 대해서 인터페이스를 통해 메서드를 호출하면, 객체의 타입과 연결된 메서드를 호출하게 된다.

~~~
public static class Program {
    public static void Main() {
        /******************************** 첫번째 예제 ********************************/
        Base b = new Base();

        // b의 타입에서 정의하는 Dispose 호출한다. "Base's Dispose"가 표시된다.
        b.Dispose();

        // b가 참조하는 객체의 타입에서 정의하는 Dispose 호출한다. "Base's Dispose"가 표시된다.
        ((IDisposable)b).Dispose();

        /******************************** 두번째 예제 ********************************/
        Derived d = new Derived();

        // d의 타입에서 정의하는 Dispose 호출한다. "Derived's Dispose"가 표시된다.
        d.Dispose();

        // d가 참조하는 객체의 타입에서 정의하는 Dispose 호출한다. "Derived's Dispose"가 표시된다.
        ((IDisposable)d).Dispose();

        /******************************** 세번째 예제 ********************************/
        b = new Derived();

        // b의 타입에서 정의하는 Dispose 호출한다. "Base's Dispose"가 표시된다.
        b.Dispose();

        // b가 참조하는 객체의 타입에서 정의하는 Dispose 호출한다. "Derived's Dispose"가 표시된다.
        ((IDisposable)b).Dispose();
    }
}

internal class Base : IDisposable {
    // 이 메서드는 암묵적으로 seald되었으며 재정의가 불가능하다.
    public void Dispose() {
        Console.WriteLine("Base's Dispose");
    }
}

// 이 클래스는 Base 클래스로부터 상속되었으며 다시 한번 IDisposable 인터페이스를 구현한다.
internal class Derived : Base, IDisposable {
    // 이 메서드는 Base 클래스의 Dispose 메서드를 재정의할 수 없다.
    // 'new' 키워드를 사용하여 이 메서드가 IDisposable 인터페이스의 Dispose 메서드를 다시 구현하고 있음을 나타낸다.
    new public void Dispose() {
        Console.WriteLine("Derived's Dispose");

        // 참고: 필요한 경우 다음 줄과 같이 코드를 작성하여 부모 클래스의 Dispose 메서드를 호출할 수 있따.
        // base.Dispose();
    }
}
~~~

### 인터페이스 메서드 호출에 대한 자세한 내용
CLR은 필드나 매개변수 혹은 로컬 변수를 인터페이스 타입으로 정의할 수 있도록 허용한다. 인터페이스 타입의 변수를 사용하면, 인터페이스에서 정의하고 있는 메서드를 호출할 수 있다. 또한 CLR은 Object가 정의하고 있는 메서드를 호출하는 것도 허용하는데, 모든 클래스들이 Object 클래스의 메서드를 상속받기 때문이다.
~~~
// s 변수는 String 객체를 가리킨다.
String s = "Jeffrey";
// s 변수를 사용하여 String, Object, IComparable, ICloneable, IConvertible, IEnumerable
// 등의 타입에 정의된 어떠한 메서드라도 호출할 수 있다.

// cloneable 변수가 동일한 String 객체를 참조하도록 한다.
IConeable cloneable = s;
// cloneable 변수를 사용하면 ICloneable 인터페이스
// 또는 Object 클래스가 정의하고 있는 메서드를 호출할 수 있다.

// comparable 변수가 동일한 String 객체를 참조하도록 한다.
IComparable comparable = s
// comparable 변수를 사용하면 IComparable 인터페이스
// 또는 Object 클래스가 정의하고 있는 메서드를 호출할 수 있다.

// enumerable 변수가 동일한 String 객체를 참조하도록 한다.
// 만일 객체가 서로 다른 두 인터페이스를 모두 구현하고 있다면
// 실행 시에 두 인터페이스 간에 양방향으로 캐스팅을 수행할 수 있다.
IEnumerable enumerable = (IEnumeable) comparable;
// enumerable 변수를 사용하면 IEnumerable 인터페이스
// 또는 Object 클래스가 정의하고 있는 메서드를 호출할 수 있다.
~~~

### 인터페이스 메서드의 암묵적 구현과 명시적 구현
~~~
internal sealed class SimpleType : IDisposable {
    public void Dispose() { Console.WriteLine("Dispose"); }
}
~~~
이 타입의 메서드 테이블에는 다음의 같은 항목들이 포함하게 된다.
* 암묵적인 기본 클래스인 Object에 정의되어 있는 모든 가상 인스턴스 메서드들이 상속된다.
* 인터페이스 상속에 의해서 IDisposable 인터페이스가 정의하고 있는 모든 메서드들이 상속된다.
* SimpleType가 새롭게 구현 Dispose 메서드가 포함된다.

개발자에게 단순화된 개념을 전달하기 위하여, C# 컴파일러는 SimpleType에 정의된 Dispose 메서드가 IDisposable의 Dispose 메서드를 구현한 것으로 가정한다. C# 컴파일러가 이렇게 가정하는 이유는 이 메서드가 public으로 선언되어 있으며 인터페이스 내의 메서드와 새로 정의한 메서드 원형이 완벽히 일치하기 때문이다. 즉 두 메서드가 동일한 매개변들과 반환 타입을 가지고 있다. 또한 새로 정의한 메서다 Dispose가 virtual로 선언되었다 하더라도, 이 메서드가 인터페이스 메서드를 구현한 것으로 해석하는 데에는 변함이 없다.

~~~
public sealed class Program {
    public static void Main() {
        SimpleType st = new SimpleType();

        // 이 메서드 호출은 public으로 선언한 Dispose 메서드를 호출한다.
        st.Dispose();

        // 이 메서드 호출은 IDisposable 인터페이스의 Dispose 메서드 구현을 호출한다.
        IDisposable d = st;
        d.Dispose();
    }
}

internal sealed class SimpleType : IDisposable {
    public void Dispose() { Console.WriteLine("Dispose"); }
    void IDisposable.Dispose() { Console.WriteLine("IDisposable Dispose"); }
}
~~~
C#에서 메서드의 이름 앞에 해당 메서드를 정의한 인터페이스 이름을 접두사로 붙여서 메서드를 정의하면, **명시적 인터페이스 구현 메서드**(Explicit Interface Method Implementation, EIMI)를 만들게 된다. 주의해야 할 것은 C#에서 이와 같이 명시적으로 인터페이스 메서드를 작성하는 경우, 이 메서드에는 public이나 private과 같은 접근자를 임의로 설정할 수 없는 제약이 있다는 것이다. 하지만 컴파일러가 이 메서드를 위한 메타데이터를 만들 때에는 항상 접근자로 private을 설정한 것으로 간주하여 인터페이스 메서드를 클래스의 인스턴스를 통해서 호출할 수 없도록 하기 위함이다. 이 경우 인터페이스 타입의 변수를 통해서만 인터페이스 메서드를 호출할 수 있다.

### 제네릭 인터페이스
C#과 CLR은 제네릭 인터페이스를 통하여 개발자들에게 다양한 기능을 제공한다.
첫째, 제네릭 인터페이스를 이용하면 컴파일 시점의 타입 안정성이 극대화 된다. 일부 인터페이스 매개변수나 반환 타입으로  Object 타입을 사용하는 경우가 있다. 이러한 인터페이스 메서드를 호출할 때에는 아무 인스턴스나 매개변수로 전달할 수 있는데, 권장하는 방식이 아니다.
~~~
private void SomeMethod1() {
    Int32 x = 1, y = 2;
    IComparable c = x;

    // ComparaTo 메서드는 Object 타입의 매개변수를 필요로 하며, Int32 타입의 변수 y를 전달해도 무방하다.
    c.ComparaTo(y);     // y 변수가 박싱 처리되어 전달된다.

    // ComparaTo 메서드는 Object 타입의 매개변수를 필요로 하며, String 타입의 문자열 "2"가 전달되어
    // 컴파일에는 성공하지만 ArgumentException 에외가 실행 시점에 발생하게 된다.
    c.ComparaTo("2");
}
~~~

다음은 제네릭 인터페이스를 사용하여 구현한 새로운 버전의 코드다.
~~~
private void SomeMethod2() {
    Int32 x = 1, y = 2;
    IComparable<Int32> c = x;

    // ComparaTo 메서드는 Int32 타입의 매개변수를 필요로 하므로 이를 전달한다.
    c.ComparaTo(y);     // y 변수가 박싱 처리되어 전달된다.

    // ComparaTo 메서드는 Int32 타입의 매개변수를 필요로 하지만, 문자열 "2"를 전달할 경우
    // String 타입은 Int32 타입으로 캐스팅할 수 없음을 알리는 컴파일 오류가 발생한다.
    c.ComparaTo("2");
}
~~~

두 번째 장점은 값 타입과 함께 사용했을 때 박싱을 훨씬 적게 사용할 수 있다는 것이다.
세 번째 장점은 동일 인터페이스를 다른 타입 매개변수를 지정하여 여러 번 구현하는 것이 가능하다는 점이다.
~~~
public sealed class Number: IComparable<Int32>, IComparable<String> {
    private Int32 m_val = 5;

    public Int32 CompareTo(Int32 n) {
        return m_val.CompareTo(n);
    }

    public Int32 CompareTo(String s) {
        return m_val.CompareTo(Int32.Parse(s));
    }
}

public static class Program {
    public static void Main() {
        Number n = new Number();

        IComparable<Int32> cInt32 = n;
        Int32 result = cInt32.CompareTo(5);

        IComparable<String> cString = n;
        result = cString.CompareTo("5");
    }
}
~~~

### 제네릭과 인터페이스 제약조건
첫 번째 이점은 단일 제네릭 타입 매개변수가 여러 인터페이스 타입을 동시에 구현하도록 제약할 수 있다는 점이다. 이렇게 하면, 여기에 지정되는 타입은 제약조건에 기술된 '모든' 인터페이스를 반드시 구현해야 한다.
~~~
public static class SomeType {
    private static void Test() {
        Int32 x = 5;
        Guid g = new Guid();

        // Int32 타입은 IComparable과 IConvertible 인터페이스를 모두 구현하므로
        // 오류가 발생하지 않는다.
        M(x);

        // Guid 타입은 IComparable 인터페이스만을 구현하고
        // IConvertible 인터페이스는 구현하지 않으므로 컴파일 시점에는 오류가 발생한다.
        M(g);
    }

    private static Int32 M<T>(T t) where T | IComparable, IConvertible {
        ...
    }
}
~~~
메서드의 매개변수를 정의할 때 생각해보면, 전달받는 매개변수는 매개변수의 정의 시 사용한 타입이거나 혹은 이를 상속한 타입이어야 한다. 매개변수의 타입을 인터페이스로 정의하였다면, 해당 인터페이스를 구현한 어떤 타입의 객체라도 매개변수가 될 수 있다. 그런데 이 예와 같이 여러 개의 인터페이스를 사용하여 타입 매개변수의 타입을 제약할 수 있기 때문에, 이를 통해 특정 메서드의 매개변수 타입을 제약사항 설정 시에 사용한 여러인터페이스를 동시에 구현한 타입으로 제한할 수 있게 된다.

인터페이스 제약조건의 두 번째 이점은 값 타입의 인스턴스를 전달할 때 발생하게 되는 박싱을 최소화할 수 있다는 것이다.

### 같은 메서드 이름과 원형을 가지는 여러 인터페이스 구현하기
두개의 서로 다른 인터페이스에서 제공하는 메서드를 각기 구현하려면 명시적 인터페이스 구현 메서드(EIMI)를 작성해야 한다.
~~~
public sealed class MarioPizzeria : IWindow, IRestaurant {
    Object IWindow.GetMenu() { ... }

    Object IRestaurant.GetMenu() { ... }

    public Object GetMenu() { ... }
}

public static class Program {
    public static void Main() {
        MarioPizzeria mp = new MarioPizzeria();

        mp.GetMenu();

        IWindow window = mp;
        window.GetMenu();

        IRestaurant restaurant = mp;
        restaurant.GetMenu();
    }
}
~~~

### 명시적 인터페이스 구현 메서드로 컴파일 시 타입 안정성 향상시키기
인터페이스를 사용하면 타입들 간에 의사소통하는 방법을 표준화할 수 있기 때문에 상당히 좋다. 비제네릭 인터페이스에서 정의하고 있는 메서드들이 매개변수나 반환 타입으로 System.Object를 사용하는 경우, 컴파일 시점의 타입 안정성을 잃게 되는 문제가 발생하고 박싱이 발생하게 된다.

~~~
public interface IComparable {
    Int32 CompareTo(Object other);
}

internal struct SomeValueType : IComparable {
    private Int32 m_x;
    public SomeValueType(Int32 x) { m_x = x; }
    public Int32 CompareTo(Object other) {
        return (m_x - ((SomeValueType) other).m_x);
    }
}

public static void Main() {
    SomeValueType v = new SomeValueType(0);
    Object o = new Object();
    Int32 n = v.CompareTo(v);
    n = v.CompareTo(o);
}
~~~
이 코드에서는 두 가지의 문제점이 있다.
* 예기치 않은 박싱: 변수 v가 CompareTo 메서드로 전달되는 과정에서 CompareTo 메서드가 Object 타입의 매개변수를 필요로 하기 때문에 반드시 박싱이 필요하다.
* 타입 안정성의 부재: 이 코드는 컴파일이 되긴 하지만, 실행 시에 o을 SomeValueType 타입으로 캐스팅하려고 하기 때문에 CompareTo 메서드 내부에서 InvalidCastException 예외가 발생하게 된다.

이 두 가지 문제점은 명시적 인터페이스 구현 메서드를 통해서 해결할 수 있다.
~~~
internal struct SomeValueType : IComparable {
    private Int32 m_x;
    public SomeValueType(Int32 x) { m_x = x; }
    public Int32 CompareTo(SomeValueType other) {
        return (m_x - other.m_x);
    }

    Int32.IComparable.CompareTo(Object other) {
        return CompareTo((SomeValueType) other);
    }
}
~~~
이제 컴파일 시점의 타입 안정성을 확보하고 더 이상 불필요한 박싱도 일어나지 않게 된다.

명시적 인터페이스 구현 메서드는 IConvertible, ICollection, IList, IDictionary 같은 인터페이스를 구현해야 하는 경우 상당히 자주 접하게 된다. 이러한 기법을 사용하면 타입 안정성이 높은 메서드를 호출하도록 코드를 작성할 수 있을 뿐 아니라 값 타입에 대한 박싱을 최소화할수도 있다.

### 명시적 인터페이스 구현 메서드에서 주의해야 할 부분
명시적 인터페이스 구현 메서드에서 가장 큰 문제점은 다음과 같은 사항들이 있다.
* 명시적 인터페이스 구현 메서드를 타입 내에서 실제로 어떻게 구현하고 있는지가 문서화되어 있지 않다.
* 값 타입의 인스턴스를 인터페이스로 캐스팅할 때 박싱이 발생한다.
* 명시적 인터페이스 구현 메서드는 상속한 타입에서 호출할 수 없다.

세 번째 문제점 아마도 가장 심각한 문제이기도 한데, 명시적 인터페이스 구현 메서드를 상속 받은 클래스에서 호출할 수 없다는 것이다.
~~~
internal class Base : IComparable {
    Int32 IComparable.CompareTo(Object o) {
        Console.WriteLine("Base's CompareTo");
        return 0;
    }
}

internal sealed class Derived : Base, IComparable {
    // 인터페이스 메서드 구현에 대응되는 public 메서드
    public Int32 CompareTo(Object o) {
        Console.WriteeLine("Derived's CompareTo");

        // 상위 클래스의 명시적 인터페이스 구현 메서드를 호출하기 위하여 다음과 같이 코드를 작성하면
        // 컴파일러 오류 CS0117: 'Base'에 'CompareTo'에 대한 정의가 없습니다. 라는 오류가 발생한다.
        base.CompareTo(o);
        return 0;
    }
}
~~~
여기서의 문제점은 Base 클래스가 public이나 protected로 선언된 CompareTo 메서드를 제공하고 있지 않으며, ICommparable 타입을 통해서만 호출할 수 있는 CompareTo 메서드만을 가지고 있기 때문이다. 이 문제를 해결하기 위하여 다음과 같이 코드를 수정하려 할 수도 있다.
~~~
// 인터페이스 메서드 구현에 대응되는 public 메서드
public Int32 CompareTo(Object o) {
    Console.WriteLine("Derived's CompareTo");

    // 이런 방법으로 부모 클래스의 명시적 인터페이스 구현 메서드를 호출하려고 하면 무한 재귀 호출이 발생한다.
    IComparable c = this;
    c.CompareTo(o);

    return 0;
}
~~~
하지만 Derived 클래스 또한 IComparable 인터페이스의 CompareTo를 구현하고 있기 때문에, 무한 재귀 호출을 일으키게 된다. 이 문제를 해결하기 위해서는 Derived 클래스가 구현해야 할 인터페이스 목록에서 IComparable 인터페이스를 제거해야 한다.

이러한 문제점을 해결하기 위한 가장 좋은 방법은 기본 클래스에서 명시적 인터페이스 구현 메서드 외에 추가적인 가상 메서드로 정의하고, 이를 상속한 Derived 클래스에서 가장 메서드를 재정의하는 것이다.
~~~
internal class Base : IComparable {
    Int32 IComparable.CompareTo(Object o) {
        Console.WriteLine("Base's IComparable CompareTo");
        return CompareTo(o);    // 이제부터는 가상 메서드를 호출한다.
    }

    // 상속한 클래스를 위한 가상 메서드 정의다. (메서드의 이름은 임의로 정할 수 있다.)
    public virtual Int32 CompareTo(Object o) {
        Console.WriteLine("Base's virtual CompareTo");
        return 0;
    }
}

internal sealed class Derived : Base, IComparable {
    // 인터페이스 구현으로도 연결되는 public 메서드다.
    public override Int32 CompareTo(Object o) {
        Console.WriteLine("Derived's CompareTo");

        // 이제 Base 클래스의 가상 메서드를 호출할 수 있다.
        return base.CompareTo(o);
    }
}
~~~
이 코드에서는 가상 메서드를 public으로 선언하였지만, 때로는 이를 protected로 변경하는 것이 더욱 좋은 경우도 있다. public 대신 protected로 변경하는 것도 나쁠 것이 없지만 추가적으로 사소한 변경이 필요할 수도 있다. 명시적 인터페이스 구현 메서드는 아주 특수한 상황에서는 유용할지 모르나 타입을 더욱 복잡하게 만들기 때문에 가능한 사용하지 않는 것이 좋다.

설계 지침: 기본 클래스 혹은 인터페이스
* IS-A vs. CAN-DO 관계: 상속한 타입과 기본 타입간에 IS-A 관계를 설정할 수 없다면, 기본 타입을 정의하지 말고 인터페이스를 사용하는 것을 고려해야 한다. 인터페이스는 CAN-DO 관계를 나타낸다. 만약 여러 객체와의 관계 속에 CAN-DO 관계가 드러난다면 인터페이스를 사용하는 것이 적당하다.
* 사용의 편의성: 개발자의 관점에서 새로운 타입을 정의할 때, 기존 타입을 상속하는 것이 인터페이스에서 정의하고 있는 메서드들을 구현하는 것보다 더 간편하다.
* 일관성 있는 구현: 인터페이스에 대한 명세와 계약이 얼마나 명료하게 문서화되어 있는지에 대한 여부와는 관계없이, 모든 사람들이 인터페이스를 완벽하게 구현할 것이라고 보긴 어렵다.
* 버전관리: 만약 기본 타입에 새롭게 메서드를 추가하게 되면 이를 상속한 타입은 새로운 메서드를 상속받게 되며, 주어진 기능을 그대로 사용할 수 있을 뿐 아니라, 소스 코드를 전혀 변경하거나 다시 컴파일하지 않아도 된다. 반면 인터페이스에 새로운 메서드를 추가하면 인터페이스를 구현한 모든 타입들은 추가된 메서드를 구현해야 할 뿐만 아니라 다시 컴파일해야 한다.