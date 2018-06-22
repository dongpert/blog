---
title: clrviacharp-chapter9
tags:
---
### 선택적 매개변수와 명명된 매개변수
~~~
public static class Program {
    private static Int32 s_n = 0;

    private static void M(Int32 x = 9, String s = "A", DateTime dt = default(DateTime), Guid guid = new Guid()) {
        Console.WriteLine("x={0}, s={1}, dt={2}, guid={3}", x, s, dt, guid);
    }
    
    public static void Main() {
        M();
        M(8, "X");
        M(5, guid: Guid.NewGuid(), dt: DateTime.Now);
        M(s_n++, s_n++.ToString());
        M(s: (s_n++).ToString, x: s_n++);
    }
}
~~~
### 규칙과 가이드라인
* 메서드, 생성자 메서드, C#의 인덱서와 같이 매개변수가 있는 속성 등을 사용할 때 매개변수에 기본값을 지정할 수 있다. 또한 델리게이트를 정의할 때도 매개변수에 기본값을 지정할 수 있다.
* 기본값이 있는 매개변수는 반드시 기본값을 지정하지 않은 매개변수들보다 순서상 뒤에 와야만 한다.
* 기본값들은 모두 컴파일 시점에서 알 수 있는 상수 값이어야 한다.
* 명명된 매개변수를 사용하는 경우 매개변수의 이름을 직접 지정하므로 매개변수의 이름을 임의로 변경하지 않도록 유의한다.
* 모듈 외부에서 불리는 메서드의 매개변수의 기본값을 바꾸는 것은 잠재적인 위험성을 내포하게 된다.
    ~~~
    // 이렇게 하지 않기 바란다.
    private static String MakePath(String filename = "Untitled") {
        return String.Format(@"C:\{0}.txt", filename);
    }

    // 대신 이렇게 만드는 것이 좋다.
    private static String MakePath(String filename = null) {
        return String.Format(@"C:\{0}.txt", filename ?? "Untitled");
    }
    ~~~
* ref나 out 키워드를 사용하는 매개변수에는 기본값을 지정할 수 없는데, 왜냐하면 이러한 매개변수로는 의미 있는 기본값을 전달할 방법이 없기 때문이다.

> 선택적 또는 명명된 매개변수를 이용하여 메서드를 호출할 때 염두에 두어야 할 규칙과 가이드라인
* 매개변수를 지정하는 순서를 임의로 변경할 수 있다. 하지만 명명된 매개변수 리스트 끝에 나타나야만 한다.
* 기본값을 가지지 않는 매개변수들을 이름을 사용하여 지정할 수는 있지만 필요한 모든 매개변수들을 빠짐없이 지정해야 컴파일러가 컴파일 할 수 있다.
* C#은 M(1, ,DateTime)처럼 매개변수의 값을 비운 채로 쉼표만 넣어 값을 생략하는 방식으로 호출할 수 없다.
* 명명된 매개변수로 ref 또는 out 키워드를 사용하려면 다음과 같은 문법을 사용해야 한다.
~~~
private static void M(ref Int32 X) { ... }

Int32 a = 5;
M(M: ref a);
~~~

### 암시적으로 타입화된 지역 변수
C#은 메서드 내의 지역 변수의 타입을 초기화 표현식으로부터 자동으로 유추할 수 있는 기능을 제공한다.
~~~
private static void ImplicitylyTypedLocalVariables() {
    var name = "Jeff";
    ShowVariableType(name);         // System.String을 표시한다.

    // var n = null;                // 오류: 암시적으로 타입화된 지역 변수에는 null을 대입할 수 없다.
                                    // null는 참조 타입 또는 Nullable<T> 값 타입 양쪽 모두로 캐스팅이 가능하기 때문이다.
    var x = (String)null;           // 유추에는 성공하지만 값을 가지지는 않는다.
    ShowVariableType(x);            // System.String을 표시한다.

    var numbers = new Int32[] { 1, 2, 3, 4 };
    ShowVariableType(numbers);

    var collection = new Dictionary<String, String>() { { "Grant", 4.0f } };
    ShowVariableType(collection);

    foreach (var item in collection) {
        ShowVariableType(item);
    }
}

private static void ShowVariableType<T>(T t) {
    Console.WriteLine(typeof(T));
}
~~~

> 지역 변수를 var 키워드로 선언하는 것은 문법적인 단축 방법일 뿐이며 컴파일러가 실제 타입을 유추하도록 명령하는 것에 지나니 않는다. var 키워드는 메서드 안에서 지역 변수를 선언할 때에만 사용할 수 있지만 dynamic 키워드는 지역 변수뿐 아니라 필드, 매개변수에까지 활용할 수 있다는 것이 다른 점이다. 그리고 var 키워드로 선언된 변수는 명시적으로 초기화가 되어야 하지만, dynamic 키워드로 선언된 변수는 그렇게 할 필요가 없다.

### 메서드에 참조로 매개변수 전달
크기가 큰 값 타입에 대해서 out을 사용하여 메서드 호출 시에 값 타입 인스턴스의 필드들이 복사되는 것을 막을 수 있기 때문에 효율적이다.
~~~
public sealed class Program {
    public static void Main() {
        Int32 x;                // x는 초기화되지 않은 상태
        GetVal(out x);
        Console.WriteLine(x);
    }

    private static void GetVal(out Int32 v) {
        v = 10;     // 이 메서드는 v를 반드시 초기화해야 한다.
    }
}
~~~

~~~
public sealed class Program {
    public static void Main() {
        Int32 x; = 5;
        GetVal(ref x);
        Console.WriteLine(x);
    }

    private static void GetVal(ref Int32 v) {
        v += 10;     // 변수 v에 들어있던 최초의 값을 읽을 수 있다.
    }
}
~~~

out이나 ref 키워드를 참조 타입과 함께 사용하면 이미 알려진 객체의 참조 값을 반환하려는 경우에만 유용하게 사용된다.
~~~
using System;
using System.IO;

public sealed class Program {
    public static void Main() {
        FileStream fs = null;
        
        ProcessingFiles(out fs);

        for (; fs != null; ProcessFiles(ref fs)) {
            fs.Read(...);
        }
    }

    private static void ProcessFiles(ref FileStream fs) {
        if (fs != null) fs.Close();

        if (noMoreFileToProcess) fs = null;
        else fs = new FileStream(...);
    }
}
~~~

제네릭을 사용한 Swap메서드
~~~
public static void Swap<T>(ref T a, ref T b) {
    T t = b;
    b = a;
    a = t;
}

public static void SomeMethod() {
    String s1 = "Jeffrey";
    String s2 = "Richter";

    Swap(ref s1, ref s2);
    Console.WriteLine(s1);
    Console.WriteLine(s2);
}
~~~

### 메서드에 가변 매개변수 전달하기
~~~
static Int32 Add(params Int32[] values) {
    Int32 sum = 0;
    if (values != null) {
        for (Int32 x = 0; x < values.Length; x++)
            sum += values[x];
    }
    return sum;
}

public static void Main() {
    Console.WriteLine(Add(new Int32[] { 1, 2, 3, 4, 5 } ));
    Console.WriteLine(Add(1, 2, 3, 4, 5));
    Console.WriteLine(Add());       // new Int32[0] 배열 객체가 전달된다.
    Console.WriteLine(Add(null));   // null이 전달되며 객체 생성이 없으므로 더 효율적이다.
}
~~~

### 매개변수 타입과 반환 타입에 대한 지침
메서드의 매개변수 타입을 정의할 때에는 가능한 가장 상위의 추상화된 타입(Weakest Type)이나 인터페이스 타입을 사용하는 것이 좋다. 예를 들어 만약 컬렉션의 아이템을 다루는 메서드를 만들어야 한다면 List<T>, ICollection<T>, IList<T>같이 구체화된 타입(Strong Type)들보다는 이보다 좀 추상적인 IEnumerable<T> 인터페이스 같은 타입을 매개변수의 타입으로 선택하는 것이 보다 바람직하다.
~~~
// 권장됨: 이 메서드는 추상화된 타입의 매개변수를 취한다.
public void ManipulateItems<T>(IEnumerable<T>, collection) { ... }

// 권장하지 않음: 이 메서드는 구체화된 타입의 매개변수를 취한다.
public void ManipulateItems<T>(List<T> collection) { ... }
~~~

이렇게 해야 하는 이유는 당연하게도 첫 번째 메서드의 경우 배열 List<T>, String과 같이 IEnumerable<T> 인터페이스를 구현하는 모든 타입들을 매개변수로 전달할 수 있기 때문이다. 두 번째 메서드에는 List<T> 타입의 객체 외의 String 타입과 같은 객체는 전혀 매개변수로 전달할 수 없다. 즉 재사용할 수 있도록 만든 첫번째 메서드가 훨씬 더 낫다

당연한 이야기이지만, 단순히 열거 가능한 객체가 아니라 정말로 리스트를 필요로 하는 경우도 있을 수 있지만, 이 경우에도 List<T> 대신 IList<T>를 사용하는 것이 좋다.

~~~
// 권장됨: 이 메서드는 추상화된 타입의 매개변수를 취한다.
public void ProcessBytes(Stream someStream) { ... }

// 권장하지 않음: 이 메서드는 구체화된 타입의 매개변수를 취한다
public void ProcessBytes(FileStream fileStream) { ... }
~~~
첫 번째 메서드는 다양한 형태의 Stream을 지원하므로, FileStream, NetworkStream, MemoryStream 등이 사용될 수 있다.

반면에, 메서드의 반환 타입을 정의할 때에는 가능한 구체적인 타입으로 정의하는 것이 좋다.
~~~
// 권장됨: 이 메서드는 구체화된 반환 타입을 사용한다.
public FileStream OpenFile() { ... }

// 권장되지 않음: 이 메서드는 추상화된 반환 타입을 사용한다.
public Stream OpenFile() { ... }
~~~

가끔은 호출하는 측의 코드에 영향을 주지 않으면서도 메서드의 내부 구현을 변경해야 할 수 있다. List<String> 객체를 반환하는 메서드의 경우라면 String[] 객체를 반환하도록 메서드를 변경하게 될 가능성이 없지 않다. 이러한 상황에서도 유연성을 유지하려면, 메서드가 반환하는 타입을 조금 더 추상화된 타입으로 정의하는 것이 좋다.
~~~
// 유연하다: 확정된 타입이 아니므로 다양한 타입을 반환할 수 있다.
public IList<String> GetStringCollection() { ... }

// 유연하지 않다: 확정된 타입을 사용하므로 반환할 수 있는 타입에 제약이 따른다.
public List<String> GetStringCollection() { ... }
~~~