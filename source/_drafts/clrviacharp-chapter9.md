---
title: clrviacharp-chapter9
tags:
---
## 선택적 매개변수와 명명된 매개변수
다음은 "선택적 매개변수"와 "명명된 매개변수"를 사용하는 예다.
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
* 모듈 외부에서 불리는 메서드의 매개변수의 기본값을 바꾸는 것은 잠재적인 위험성을 내포하게 된다. 호출하는 쪽에서는 메서드 호출 코드에 매개변수의 기본값을 포함한다. 만약 매개변수의 기본값을 변경하였지만, 호출하는 쪽의 코드를 다시 컴파일하지 않았다면, 예전의 기본값을 이용하여 메서드가 된다. 이런 문제를 예방하려면 기본값을 임의의 상수 값 대신 0 또는 null로 설정하는 것이 좋은데, 이렇게 하면 호출하는 쪽의 코드를 다시 컴파일하지 않아도 기본값을 내부적으로 관리할 수 있어 훨씬 더 편리하다. 다음은 이를 반영한 예제 코드다.
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
* ref나 out 키워드를 사용하는 매개변수에는 기본값을 지정할 수 없는데, 왜냐하면 이러한 매개변수로는 의미 있는 기본값을 전달할 방법이 없기 때문이다. 또한 선택적 또는 명명된 매개변수를 이용하여 메서드를 호출할 때 염두에 두어야 할 규칙과 가이드라인도 있다.
    * 매개변수를 지정하는 순서를 임의로 변경할 수 있다. 하지만 명명된 매개변수 리스트 끝에 나타나야만 한다.
    * 기본값을 가지지 않는 매개변수들을 이름을 사용하여 지정할 수는 있지만 필요한 모든 매개변수들을 빠짐없이 지정해야 컴파일러가 컴파일 할 수 있다.
    * C#은 M(1, ,DateTime)처럼 매개변수의 값을 비운 채로 쉼표만 넣어 값을 생략하는 방식으로 호출할 수 없다.
    * 명명된 매개변수로 ref 또는 out 키워드를 사용하려면 다음과 같은 문법을 사용해야 한다.
~~~
private static void M(ref Int32 X) { ... }

Int32 a = 5;
M(M: ref a);
~~~

## 암시적으로 타입화된 지역 변수
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

> **중요**: 지역 변수를 var 키워드로 선언하는 것은 문법적인 단축 방법일 뿐이며 컴파일러가 실제 타입을 유추하도록 명령하는 것에 지나니 않는다. var 키워드는 메서드 안에서 지역 변수를 선언할 때에만 사용할 수 있지만 dynamic 키워드는 지역 변수뿐 아니라 필드, 매개변수에까지 활용할 수 있다는 것이 다른 점이다. 그리고 var 키워드로 선언된 변수는 명시적으로 초기화가 되어야 하지만, dynamic 키워드로 선언된 변수는 그렇게 할 필요가 없다.

## 메서드에 참조로 매개변수 전달
CLR은 값으로 매개변수를 전달하는 것 외에 참조로 매개변수를 전달할 수 있다. C#에서는 이를 위해서 out과 ref 키워드를 이용할 수 있다. 양쪽 키워드는 모두 C# 컴파일러가 해당 매개변수에 대하여 참조를 전달받도록 메타데이터를 생성하게 되며, 컴파일러는 매개변수 그 자체가 아니라 매개변수의 주소 값을 이용하는 코드가 생성된다.

CLR 관점에서 보면, out과 ref 키워드의 의미는 동일한 의미다. 하지만 C# 컴파일러는 두 키워드 사이를 엄격하게 다른 의미로 취급하고 있으며, 그 차이는 메서드가 전달받은 매개변수의 초기화 의무가 있는가에 대한 부분이다. 만약 메서드의 매개변수가 out으로 지정되어 있었다면, 메서드를 호출하는 쪽에서는 변수를 초기화하지 않고 전달할 수 있으며, 메서드 내에서는 이렇게 전달되는 매개변수의 값을 읽을 수 없고, 메서드 밖으로 벗어나기 전에 반드시 이 매개변수에 값을 설정해야만 한다. 만약 메서드의 매개변수에 ref 키워드가 지정되어 있었다면 호출하는 쪽에서는 변수를 전달하기 전에 반드시 초기화를 해야만 하며, 메서드 안에서는 이 매개변수를 통해 값을 읽거나 쓸수 있다.

참조와 값 타입은 out과 ref 키워드를 지정했을 때 매우 다른게 동작한다. 우선 out과 ref 키워드를 값 타입에 붙였을 경우를 살펴보자.
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
크기가 큰 값 타입에 대해서 out을 사용하여 메서드 호출 시에 값 타입 인스턴스의 필드들이 복사되는 것을 막을 수 있기 때문에 효율적이다.

이제 out 키워드 대신 ref 키워드를 사용하는 예를 한번 더 살펴보도록 하자.
~~~
public sealed class Program {
    public static void Main() {
        Int32 x; = 5;
        AddVal(ref x);
        Console.WriteLine(x);
    }

    private static void AddVal(ref Int32 v) {
        v += 10;     // 변수 v에 들어있던 최초의 값을 읽을 수 있다.
    }
}
~~~

참조 타입의 경우, 호출하는 측은 참조 객체를 가리키는 포인터를 메모리에 할당하고, 호출되는 측은 이 포인터를 조작할 수 있다. 이러한 동작 때문에, out이나 ref 키워드를 참조 타입과 함께 사용하면 이미 알려진 객체의 참조 값을 반환하려는 경우에만 유용하게 사용된다.
~~~
using System;
using System.IO;

public sealed class Program {
    public static void Main() {
        FileStream fs = null;       // fs는 초기화되지 않았다.
        
        // 처리할 첫 번째 파일을 연다.
        StartProcessingFiles(out fs);

        // 처리할 파일이 더 있는지 확인하여 계속 반복한다.
        for (; fs != null; ContinueProcessFiles(ref fs)) {
            // 파일을 처리한다.
            fs.Read(...);
        }
    }

    private static void StartProcessingFiles(out FileStream fs) {
        fs = new FileStream(...);   // 이 메서드에서는 fs가 반드시 초기화 되어야 한다.
    }

    private static void ContinueProcessFiles(ref FileStream fs) {
        fs.Close();

        if (noMoreFileToProcess) fs = null;
        else fs = new FileStream(...);
    }
}
~~~
보시다시피 out과 ref 키워드가 지정된 참조 타입의 매개변수를 취하는 메서드의 가장 큰 차이점은 이 메서드가 새로운 객체를 생성하고 새로운 객체를 가리키는 포인터를 호출하는 측으로 반환한다는 것이다. 앞의 코드를 다음과 같이 단순하게 쓸수도 있다.
~~~
using System;
using System.IO;

public sealed class Program {
    public static void Main() {
        FileStream fs = null;       // null로 꼭 설정해주어야 한다.
        
        // 처리할 첫 번째 파일을 연다.
        ProcessingFiles(ref fs);

        // 처리할 파일이 더 있는지 확인하여 계속 반복한다.
        for (; fs != null; ProcessFiles(ref fs)) {
            // 파일을 처리한다.
            fs.Read(...);
        }
    }

    private static void ProcessFiles(ref FileStream fs) {
        // 만약 파일이 열렸던 적이 있다면 파일을 닫는다.
        if (fs != null) fs.Close();

        // 처리할 다음 파일을 열거나, 처리할 파일이 없으면 null을 반환한다.
        if (noMoreFileToProcess) fs = null;
        else fs = new FileStream(...);
    }
}
~~~
제네릭을 사용하도록 하면 여러분이 원하는 대로 잘 작동하는 코드를 만들 수 있다. Swap 메서드를 다음과 같이 수정할 수 있다.
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
가변 매개변수를 받을 수 있도록 메서드를 정의하는 방법은 다음과 같다.
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
> **중요**: 메서드에 가변 매개변수를 전달하는 경우에는 null을 전달하지 않는 한 추가적인 성능 저하가 있을 수 있음을 알고 있어야 한다. 결국 힙에는 배열 객체가 반드시 할당되어야 하고, 배열의 각 요소들 역시 초기화되어야 하며 궁극적으로 배열의 메모리들도 가비지 컬렉션의 대상이 될 것이기 때문이다. 성능 상의 손해를 최소화하기 위해서는 params 키워드를 사용한 메서드가 호출 되지 않도록 자주 사용될 가능성이 있는 패턴들을 이미 오버로드의 형태로 구현해주는 것이 좋다.

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

참고로 앞의 예제에서 살펴본 컬렉션들은 인터페이스 아키텍처를 이용한 설계 규칙에 따라 정의된 컬렉션에 대한 것들이다. 하지만 상속의 특성을 이용하는 클래스 아키텍처 설계 규칙에 따라 정의된 클래스들을 사용하는 경우에도 동일한 규칙을 사용할 수 있다. 예를 들어 스트림으로부터 바이트를 읽어 처리하는 메서드를 작성하려는 경우, 다음과 같이 코드를 작성할 수 있다.
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