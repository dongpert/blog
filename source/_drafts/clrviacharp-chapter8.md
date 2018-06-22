---
title: clrviacharp-chapter8
tags:
---
## 확장메서드
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
1. StringBuilder 타입에서 문자의 위치를 얻어오는 로직을 사용하고 싶다면 StringBuilderExtensions 클래스에 이 로직이 들어있다는 사실을 알아야만 한다.
2. 이 메서드가 StringBuilder 인스턴스의 실제 실행 순서를 반영하지 못하기 때문에 코드를 읽고 쓰기 어려움은 물론 알아보기도 어렵게 만든다.

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

### 확장 메서드로 여러 타입을 확장하기
확장 메서드가 정적 메서드에 대한 호출에 불과하기 때문에, CLR은 호출 과정에서 전달되는 객체가 null인지 검사하는 코드를 내보내지 않는다
~~~
StringBuilder sb = null;
// IndexOf 메서드를 호출했다고 해서 곧바로 NullReferenceException이 발생하지는 않으며
// 메서드의 반복문에서 비로소 NullReferenceException이 발생하게 될 것이다.
sb.IndexOf('X');
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
            throw new ArgumentNullException("value);
    }
}
~~~
부분 메서드를 구현하지 않고 그냥 둘 경우, 컴파일러는 부분 메서드에 대한 메타데이터를 일절 포함시키지 않는다. 더 나아가서 컴파일러는 부분 메서드를 호출하는 코드 자체를 모두 제거한다.

### 규칙과 가이드라인
* 부분 메서드는 부분 클래스나 부분 구조체 위에서만 정의될 수 있다.
* 부분 멤서드는 반드시 반환 타입이 void여야만 하며 out 한정자를 붙인 매개변수를 사용할 수 없다. 매개변수에 ref 한정자를 붙이거나, 제네릭으로 선언하거나, 인스턴스 또는 정적 메서드이거나, unsafe 한정자를 붙이는 것을 허용한다.
* 부분 메서드를 정의하고 부분 메서드를 구현할 때 양쪽의 원형은 동일해야 한다.
* 부분 메서드는 항상 private 키워드를 가진 메서드로 취급된다.

