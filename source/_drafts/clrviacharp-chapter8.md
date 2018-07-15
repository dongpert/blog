---
title: clrviacharp-chapter8
tags:
---
## 인스턴스 생성자와 클래스 (참조 타입)
**생성자**는 특별한 유형의 메서드로 타입의 인스턴스를 올바른 상태로 초기화하는 것을 돕는다. 참조 타입으로 객체의 인스턴스를 생성하면, 인스턴스의 데이터 필드들을 저장하기 위해서 메모리 할당되는데, 먼저 객체의 오버헤드 필드(타입 객체를 가리키는 포인터와 동기화 블록 인덱스)가 초기화되고, 그 다음으로 타입의 인스턴스 생성자가 호출되어 객체의 초기 상태를 설정하게 된다.

만약 클래스를 정의할 때 명시적으로 어떠한 생성자도 만들지 않았다면, C# 컴파일러는 매개변수 없는 기본 생성자를 자동으로 정의하여 이를 호출할 수 있도록 해준다.

만약 인스턴스 필드를 초기화하는 인라인 코드가 여럿 있고, 오버로드된 생성자를 많이 만들어야 한다면, 필드 초기화 구문 대신, 공통적인 초기화 작업을 수행하는 단일의 생성자를 생성하는 것이 좋다. 이렇게 하면 생성되는 코드의 크기를 줄일 수 있다. 다음은 C#이 제공하는 this 키워드를 이용하여 현재 클래스 내에 정의되어 있는 다른 생성자를 명시적으로 호출하는 방법을 보여준다.

~~~
internal sealed class SomeType {
    private Int32 m_x;
    private String m_s;
    private Double m_d;
    private Byte m_b;

    // 이 생성자에서는 모든 필드를 기본값으로 설정하고 있다.
    // 나머지 다른 생성자들은 이 생성자를 명시적으로 호출하게 된다.
    public SomeType() {
        m_x = 5;
        m_s = "Hi there";
        m_d = 3.14159;
        m_b = 0xff;
    }

    public SomeType(Int32 x) : this() {
        m_x = x;
    }

    public SomeType(String s) : this() {
        m_s = s;
    }

    public SomeType(Int32 x, String s) : this() {
        m_x = x;
        m_s = s;
    }
}
~~~

## 인스턴스 생성자와 구조체 (값 타입)
값 타입(구조체) 생성자는 참조 타입(클래스) 생성자와는 다르게 동작한다. CLR에서는 값 타입의 인스턴스를 언제든 생성할 수 있도록 허용하고 있으므로, 이를 막을 방법이 없다. 이런 이유로 값 타입은 내부에 생성자를 정의할 필요가 없으며, C# 컴파일러에는 값 타입에 대해서는 매개변수가 없는 기본 생성자 코드를 생성하지 않는다.

CLR은 값 타입에 대해서도 생성자를 정의할 수 있도록 허용하고 있다. 이렇게 정의한 생성자를 실행하는 유일한 방법은 명시적으로 생성자를 호출하는 방법뿐이며, 다음에 그 방법을 나타냈다.
~~~
internal struct Point {
    public Int32 m_x, m_y;

    public Point(Int32, Int32 y) {
        m_x = x;
        m_y = y;
    }
}

internal sealed class Rectangle {
    public Point m_topLeft, m_bottomRight;

    public Rectangle() {
        m_topLeft = new Point(1, 2);
        m_bottomRight = new Point(100, 200);
    }
}
~~~

## 타입 생성자
인스턴스 생성자에 외에도, CLR은 타입 생성자(혹은 **정적 생성자**, **클래스 생성자**, **타입 초기자** 등으로도 불린다)를 지원한다. 타입 생성자는 인터페이스에 대해서도 적용이 가능하며(C#에서는 허용되지 않지만), 참조 타입과 값 타입에 대해서도 사용할 수 있다. 타입은 내부적으로 사용하는 기본 생성자가 따로 없다. 만약 타입 생성자를 정의해야 하는 경우라면 타입당 하나만 정의할 수 있으며 매개변수를 가질 수 없다. 다음의 코드는 C#에서 참조 타입과 값 타입 각각에 타입 생성자를 정의하는 방법을 나타내고 있다.
~~~
internal sealed class SomeRefType {
    static SomeRefType() {
        // SomeRefType 타입을 처믕 사용하는 경우 이 타입 생성자가 실행된다.
    }
}
internal struct SomeValType {
    static SomeValType() {
        // SomeValType 타입을 처믕 사용하는 경우 이 타입 생성자가 실행된다.
    }
}
~~~
타입 생성자를 정의하는 것은 매개변수가 없는 인스터스 생성자를 정의하는 것과 유사하게 보이지만, 단 하나 다른 차이점은 static 키워드를 사용해서 정의해야 한다는 것이다. 또한 타입 생성자는 반드시 private이어야 하는데, C#이 자동으로 private를 붙여준다.

타입 생성자 안에서는 오로지 타입 내의 정적 필드에만 접근이 가능하며, 주로 이러한 필드들을 초기화하기 위한 목적으로 작성된다.

## 변환연산자
**변환 연산자**는 어떤 타입에서 다른 타입으로의 변환을 담당하는 메서드다. 그리고 변환 연산자 메서드는 특별한 문법을 사용하여 정의할 수 있다. CLR 사양에서는 이러한 변환 연산자 메서드가 반드시 public 접근자로 선언되어야 하고 정적 메서드로 정의되어야 한다고 강제한다. C#을 포함한 다른 모든 언어들은 매개변수의 타입이나 메서드의 반환 타입 둘 중 하나는 변환 연산자 메서드가 정의된 타입과 동일한 타입이어야 한다는 것도 같이 정하고 있다. 이렇게 규칙을 두는 이유는 C# 컴파일러가 변환 연산자를 좀 더 빠른 시간 안에 찾아 바인딩할 수 있도록 돕기 위한 취지다. 다음의 코드는 Rational 타입에 대해 새로 추가한 네 개의 변환 연산자 메서드의 예시다
~~~
public sealed class Rational {
    public Rational(Int32 num) { ... }

    public Rational(Single num) { ... }

    public Int32 ToInt32() { ... }
    
    public Single ToSingle() { ... }

    // Int32 타입으로부터 Rational 타입을 암묵적으로 만들도록 한다.
    public static implicit operator Rational(Int32 num) {
        return new Rational(num);
    }

    public static implicit operator Rational(Single num) {
        return new Rational(num);
    }

    // Rational 타입에서 Int32 타입으로 명시적 캐스팅을 할 수 있게 한다.
    public static explicit operator Int32(Rational r) {
        return r.ToInt32();
    }

    public static explicit operator Single(Rational r) {
        return r.ToSingle();
    }
}
~~~

## 확장메서드
C#의 **확장 메서드**를 이해하는 가장 좋은 방법은 예제를 통해서 이해하는 것이다. 예를 들어 다음과 같이 IndexOf 메서드를 새로 정의하려 한다고 가정하자.
~~~
public static class StringBuilderExtensions {
    public static Int32 IndexOf(StringBuilder sb, Char value) {
        for (Int32 index = 0; index < sb.Length; index++)
            if (sb[index] == value) return index;
        return -1;
    }

    StringBuilder sb = new StringBuilder("Hello. My name is Jeff.");
    Int32 index = StringbuilderExtensions.IndexOf(sb.Replace('.', '!'), '!');
}
~~~
이 코드는 매우 잘 작동하지만, 개발자의 관점에서는 별로 이상적이지 못하다. 첫째, StringBuilder 타입에서 문자의 위치를 얻어오는 로직을 사용하고 싶다면 StringBuilderExtensions 클래스에 이 로직이 들어있다는 사실을 알아야만 한다. 둘째, 이 메서드가 StringBuilder 인스턴스의 실제 실행 순서를 반영하지 못하기 때문에 코드를 읽고 쓰기 어려움은 물론 알아보기도 어렵게 만든다.

우리는 직접 고유한 IndexOf 메서드를 정의하여 앞의 문제점들을 쉽게 해결할 수 있다. IndexOf 메서드를 확장 메서드로 만들기 위해서는 단순히 this 키워드를 첫 번째 매개변수에 붙이기만 하면 된다.
~~~
public static class StringBuilderExtensions {
    public static Int32 IndexOf(this StringBuilder sb, Char value) {
        for (Int32 index = 0; index < sb.Length; index++)
            if (sb[index] == value) return index;
        return -1;
    }
}

Int32 index = sb.Replace('.', '!').IndexOf('!');
~~~

### 규칙과 가이드라인
확장 메서드에 대해서 알아야 할 몇 가지 규칙과 따라야 할 가이드라인이 있다.
* C#은 확장 메서드만을 지원하며, 확장 속성, 확장 이벤트, 확장 연산자 같은 개념은 사용할 수 없다.
* 확장 메서드는 반드시 제네릭이 아닌 정적 클래스 위에서 정의되어야만 한다. 하지만 클래스의 이름에는 제약이 없어서, 여러분이 어떻게 작명하든 전혀 문제가 되지 않는다. 물론 확장 메서드는 최소한 하나 이상의 매개변수를 가져야 하고, 매개변수들 중 처음 오는 매개변수는 반드시, 그리고 오는 매개변수만이 this 키워드로 표시될 수 있다.
* C# 컴파일러는 확장 메서드를 파일 수준에서 찾을 수 있는 정적 클래스에서만 검색한다.
* 확장 메서드는 잠재적인 버전 관리 문제를 내포하고 있다.

### 확장 메서드로 여러 타입을 확장하기
확장 메서드가 정적 메서드에 대한 호출에 불과하기 때문에, CLR은 호출 과정에서 전달되는 객체가 null인지 검사하는 코드를 내보내지 않는다
~~~
StringBuilder sb = null;

// IndexOf 메서드를 호출했다고 해서 곧바로 NullReferenceException이 발생하지는 않으며
// 메서드의 반복문에서 비로소 NullReferenceException이 발생하게 될 것이다.
sb.IndexOf('X');

// 인스턴스 메서드 Replace를 부르는 즉시 NullReferenceException이 발생하게 될 것이다.
sb.Replace('.', '!');
~~~

인터페이스 타입에 대해서도 정의할 수 있다
~~~
public static void ShowItems<T>(this IEnumerable<T> collection) {
    foreach (var item in collection)
        Console.WriteLine(item);
}

public static void Main() {
    "Grant".ShowItems();
    new [] { "Jeff", "Kristin" }.ShowItems();
    new Lit<Int32>() { 1, 2, 3 }.ShowItems();
}
~~~

델리게이트 타입에 대해서도 확장 메서드를 정의할 수 있다.
~~~
public static void InvokeAndCatch<TException>(this Action<Object> d, Object o) where TException : Exception {
    try { d(o); }
    catch (TException) { }
}

Action<Object> action = o => Console.WriteLine(o.GetType());
action.InvokeAndCatch<NullReferenceException>(null);
~~~

## 부분메서드
클래스의 동작을 재정의하면서 여러분이 직접 클래스를 만들고, 기본 클래스로 부터 상속받아, 임의의 가상 메서드를 재정의하여 여러분이 원하는 동작을 추가했다고 가정하자. 다음 코드는 그 예시다.
~~~
internal sealed partial class Base {
    private String m_name;

    partial void OnNameChanging(String value) {

    }

    public String Name {
        get { return m_name; }
        set {
            OnNameChanging(value.Upper());
            m_name = value;
        }
    }
}

internal class Derived : Base {
    protected override void OnNameChanging(String value) {
        if (String.IsNullOrEmpty(value))
            throw new ArgumentNullException("value");
    }
}
~~~
안타깝게도 이 코드에는 두 가지 문제점이 있다.
* 클래스로 정의되는 타입은 봉인 타입이어서는 안 된다. 봉인 클래스나 암묵적으로 봉인 타입으로 분류되는 값 타입에 대해서는 이와 같은 코드를 작성할 수 없다. 더 나아가서 정적 메서드에 대해서는 이러한 동작을 사용할 수 없는데 정적 메서드는 재정의가 불가능하기 때문이다.
* 효율성에도 문제가 있다. 메서드 하나를 재정의하기 위해서 새로운 타입을 또 하나 추가한 셈인데, 이 때문에 사소하지만 시스템 자원 낭비가 발생한다.

C#의 부분 메서드 기능은 이와 같이 기능을 재정의해야 하는 상황에서 유연하게 사용할 수 있는 새로운 옵션이다.
~~~
internal sealed partial class Base {
    private String m_name;

    partial void OnNameChanging(String value);

    public String Name {
        get { return m_name; }
        set {
            OnNameChanging(value.Upper());
            m_name = value;
        }
    }
}

internal sealed partial class Base() {
    partial void OnNameChanging(String value) {
        if (String.IsNullOrEmpty(value))
            throw new ArgumentNullException("value");
    }
}
~~~
새로운 버전의 코드에서 주목할 만한 내용들은 다음과 같다.
* 반드시 그렇게 해야 할 필요가 있는 것은 아니지만 클래스를 봉인된 상태로 만들 수 있다. 사실 이 클래스는 정적 클래스는 물론 값 타입으로 변경되어도 문제가 없다.
* 도구가 생성한 코드와 개발자가 생성한 코드가 실제로 분할된 코드이고 나중에 컴파일러에 의하여 하나의 완전한 타입으로 변환된다.
* 도구에서 생성한 코드에서 부분 메서드에 대한 정의를 포함하고 있다. 이 메서드는 partial 토큰이 지정되어있고 본문이 없다.
* 개발자가 생성한 코드에서 부분 메서드의 동일한 정의에 partial 토큰이 지정되어있고 여기에 실제 본문이 들어있다.

부분 메서드를 구현하지 않고 그냥 둘 경우, 컴파일러는 부분 메서드에 대한 메타데이터를 일절 포함시키지 않는다. 더 나아가서 컴파일러는 부분 메서드를 호출하는 코드 자체를 모두 제거한다.

### 규칙과 가이드라인
* 부분 메서드는 부분 클래스나 부분 구조체 위에서만 정의될 수 있다.
* 부분 멤서드는 반드시 반환 타입이 void여야만 하며 out 한정자를 붙인 매개변수를 사용할 수 없다. 매개변수에 ref 한정자를 붙이거나, 제네릭으로 선언하거나, 인스턴스 또는 정적 메서드이거나, unsafe 한정자를 붙이는 것을 허용한다.
* 부분 메서드를 정의하고 부분 메서드를 구현할 때 양쪽의 원형은 동일해야 한다.
* 부분 메서드는 항상 private 키워드를 가진 메서드로 취급된다.

