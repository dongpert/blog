---
title: clrviacharp-chapter12
tags:
---
제네릭은 개발자들에게 여러 이점을 제공한다.
* 소스 코드 보호: 제네릭 알고리즘을 사용하기 위해서 알고리즘을 구현하고 있는 소스 코드가 반드시 필요한 것은 아니다.
* 타입 안정성: 특정한 데이터 타입 인자로 지정하여 제네릭 알고리즘 사용하면 컴파일러와 CLR은 지정된 데이터 타입과 호환되는 타입에 대해서만 해당 알고리즘을 사용할 수 있도록 해준다.
* 간결한 코드: 컴파일러가 타입 안정성을 강력하게 검사하기 때문에 소스 코드 내에 캐스팅이 거의 필요하지 않아 코드를 좀 더 작성하기 쉽고, 이해하기 쉽고, 유지 보수하기도 쉽게 된다.
* 더 나은 성능: 제네릭 도입 이전에는 일반화된 알고리즘을 정의하기 위해서 모든 멤버들을 Object 데이터 타입으로 다루어야만 했다.이러한 알고리즘을 값 타입의 인스턴스와 함게 사용하게 되면, 값 타입의 인스턴스를 알고리즘에 전달하기 위해 CLR 내부적으로 박싱을 수행해야만 했다. 박싱을 수행하면 관리 힙에 새로운 메모리를 할당하기 때문에 더 많은 가비지 수집을 유발할 수 있으며, 이로 인해 궁극적으로 성능에 나쁜 영향을 끼친다.

~~~
public static class Progrm {
    public static voide Main() {
        ValueTypePertTest();
        ReferenceTypePerfTest();
    }

    private static void ValueTypePertTest() {
        const Int32 count = 100000000;

        using (new OperationTimer("List<Int32>")) {
            List<Int32> l = new List<Int32>();
            for (Int32 n = 0; n < count; n++) {
                l.Add(n);
                Int32 x = l[n];
            }
            l = null;   // 가비지 수집기에 의해 메모리 회수가 발생하도록 한다.
        }

        using (new OperationTimer("ArrayList of Int32")) {
            ArrayList a = new ArrayList();
            for (Int32 n = 0; n < count; n++) {
                a.Add(n);
                Int32 x = (Int) a[n];
            }
            a = null;   // 가비지 수집기에 의해 메모리 회수가 발생하도록 한다.
        }
    }

    private static void ReferenceTypePerfTest() {
        const Int32 count = 100000000;

        using (new OperationTimer("List<String>")) {
            List<String> l = new List<String>();
            for (Int32 n = 0; n < count; n++) {
                l.Add("X");         // 참조 복사
                String x = l[n];    // 참조 복사
            }
            l = null;   // 가비지 수집기에 의해 메모리 회수가 발생하도록 한다.
        }

        using (new OperationTimer("ArrayList of Int32")) {
            ArrayList a = new ArrayList();
            for (Int32 n = 0; n < count; n++) {
                a.Add("X");                 // 참조복사
                String x = (String a[n];    // 타입변환 및 참조 복사
            }
            a = null;   // 가비지 수집기에 의해 메모리 회수가 발생하도록 한다.
        }
    }

    // 실행 속도를 측정하기 위하여 사용할 수 있는 유틸리티 클래스다.
    internal sealed sealed class OperationTimer : IDisposable {
        private Stopwatch m_stopwatch;
        private String m_text;
        private Int32 m_collectionCount;

        public OperationTimer(String text) {
            PrepareForOperation();

            m_text = text;
            m_collectionCount = GC.CollectionCount(0);

            m_stopwatch = Stopwatch.StartNew();
        }

        public void Dispose() {
            Console.WriteLine("{0} (GCs={1,3}) {2}", (m_stopwatch.Elapsed), GC.CollectionCount(0) - m_collectionCount, m_text);
        }

        private static void PrepareForOperation() {
            GC.Collect();
            GC.WaitForPendingFinalizers();
            GC.Collect();
        }
    }
}
~~~

## 제네릭 하부 구조
### 열린 타입과 닫힌 타입
CLR이 응용프로그램에서 사용하는 모든 타입에 대해서 개별적으로 내부적인 자료 구조를 생성한다고 논의한 바 있다. 이 자료 구조를 **타입 객체**(Type Object)라고 부른다. 제네릭 타입 매개변수를 가지는 타입도 당연히 타입으로 간주되기 때문에, CLR은 이를 표현하기 위해서 각각의 타입 객체를 만들며, 제네릭 타입 매개변수를 가질 수 있는 참조 타입(클래스), 값 타입(구조체), 인터페이스 타입, 델리게이트 타입이 그 대상이다. 각각의 타입에 대해서 제네릭 타입 매개변수가 등장하는 타입을 **열린 타입**(Open Type)이라고 한다. CLR은 열린 타입에 대해서는 인스턴스 생성을 허용하지 않는다.

제네릭 타입을 참조할 때 제네릭 타입 인자들을 지정할 수 있는데, 이 과정에서 모든 타입 인자들은 실제 데이터 타입으로 지정하게 되면, 이를 **닫힌 타입**(Closed Type)이라고 한다. CLR은 닫힌 타입에 대해서는 인스턴스 생성을 허용한다.

### 제네릭 타입과 상속
제네릭 타입을 사용할때 타입인자를 지정하면, CLR 입장에서는 새로운 타입을 정의하는 것과 같다. 이처럼 새롭게 만들어지는 타입 객체는 재네릭 타입이 상속하였던 타입을 상속한 형태가 된다. 즉 LIst<T> 타입이 Object 타입을 상속하기 때문에, 당연히 List<String>나 List<Guid>도 Object 타입을 상속하는 것이다.

~~~
internal sealed class Node<T> {
    public T m_data;
    public Node<T> m_next;

    public Node(T data) : this(data, null) {
    }

    public Node(T data, Node<T> next) {
        m_data = data; m_next = next;
    }

    public override String ToString() {
        return m_data.ToString() + ((m_next != null) ? m_next.ToString() : String.Empty);
    }
}

private static void SameDataLinkedList() {
    Node<Char> head = new Node<Char>('C');
    head = new Node<Char>('B', head);
    head = new Node<Char>('A', head);
    Console.WriteLine(head.ToString());
}
~~~

서로 다른 Char 노드, DateTime 노드, String 노드를 연결하여 하나의 연결 리스트를 만들어야 한다면 비제네릭 Node 클래스를 정의한 후, 이 클래스를 상속하여 TypeNode 클래스를 작성하는 것이다. 이렇게 하면, 연결 리스트를 작성할 때 개별 노드들을 Object가 아닌 좀 더 구체화된 타입으로 생성할 수 있으며, 컴파일 시점에서의 타입 안정성을 지킬 수 있으며, 값 타입에 대한 불필요한 박싱 연산도 막을 수 있다.

~~~
internal class Node {
    protected Node m_next;

    public Node(Node next) {
        m_next = next;
    }
}

internal sealed class TypedNode<T> : Node {
    public T m_data;

    public TypedNode(T data) : this(data, null) {
    }

    public TypedNode(T data, Node next) : base(next) {
        m_data = data;
    }

    public override String ToString() {
        return m_data.ToString() + ((m_next != null) ? m_next.ToString() : String.Empty);
    }
}

private static void DifferentDataLinkedList() {
    Node head = new TypedNode<Char>('.');
    head = new TypedNode<DateTime>(DateTime.Now, head);
    head = new TypedNode<String>("오늘은 ", head, " 입니다.");
    Console.WriteLine(head.ToString());
}
~~~

### 코드 폭증
제네릭 타입 매개변수를 사용하는 메서드를 JIT 컴파일하게 되면, CLR은 메서드의 IL 코드를 가져와 지정된 타입 인자로 대체한 후, 해당 타입을 사용하는 네이티브 코드를 생성한다. 그런데 이러한 동작 방식에는 좋지 않은 측면도 있는데, CLR이 모든 메서드/타입의 조합별로 네이티브 코드를 생성해 두어야 한다는 것이다. 이러한 현상을 **코드 폭증**(code explosion)이라고 이야기 한다.

### 공변성과 반공변성 타입 매개변수를 사용하는 델리게이트와 인터페이스
* 고정(Invariant): 제네릭 타입 매개변수는 변경될 수 없음을 의미한다.
* 반공변성(Contra-Variant): 제네릭 타입 매개변수로 지정한 타입을 해당 타입을 상속한 타입으로 변경할 수 있음을 의미한다. C#에서는 in 키워드를 사용하여 반공변성을 적용하도록 제네릭 매개변수를 설정할 수 있다.
* 공변성(Covariant): 제네릭 타입 매개변수로 지정한 타입을 해당 타입이 상속한 타입으로 변경할 수 있음을 의미한다. C#에서는 out 키워드를 사용하여 공변성을 적용하도록 제네릭 매개변수를 설정할 수 있다.

### 검증 가능성과 제약조건
제네릭 코드를 컴파일할 때 C# 컴파일러는 현재 사용 중인 타입뿐 아니라 향후 사용할 타입에 대하여서도 모든 가능성을 분석하고 검토한다.
~~~
private static Boolean MethodTakingAnyType<T>(T o) {
    T temp = o;
    Console.WriteLine(o.ToString());
    Boolean b = temp.Equals(o);
    return b;
}
~~~
이 메서드는 내부적으로 T 타입의 임시 변수 temp를 정의하고 있으며, 이 변수에 대한 대입과 메서드 호출을 수행하고 있다. 이 메서드는 모든 타입에 대해 잘 작동할 것이다. 왜냐하면 이 메서드 안에서 T 타입을 이용하여 호출하는 메서드는 Object 타입에서 정의한 ToString과 Equals 메서드뿐이기 때문이다.

~~~
private static T Min<T>(T o1, T o2) {
    if (o1.CompareTo(o2) < 0) return o1;
    return o2;
}
~~~
Min 메서드는 o1변수에 대해 CompareTo 메서드를 호출하려 한다. 하지만 많은 타입들이 CompareTo 메서드를 제공하지 않기 때문에, C# 컴파일러는 이 코드가 모든 타입에 대해서 잘 작동할 것이라고 보장할 수 없다.

제약조건은 제네릭 매개변수로 지정할 수 있는 타입의 종류를 제한하는 방법이다.
~~~
private static T Min<T>(T o1, T o2) where T : IComparable<T> {
    if (o1.CompareTo(o2) < 0) return o1;
    return o2;
}
~~~
이 코드에서 C#의 where 토큰은 컴파일러에 타입 매개변수 T에 올 수 있는 타입을 제네릭 버전의 IComparable 인터페이스를 구현한 타입으로 제한한다.

CLR은 타입 매개변수의 이름이나 제약조건을 기반으로는 오버로딩을 허용하지 않으며, 단지 메서드 인자의 개수에 의해서만 오버로딩을 할 수 있다.
~~~
// 다음과 같이 타입을 선언해도 무방하다
internal sealed class AType {}
internal sealed class AType<T> {}
internal sealed class AType<T1, T2> {}

// 오류: 제약조건이 없는 Atype<T>와 충돌한다.
internal sealed class AType<T> where T : IComparable<T> {}

// 오류: Atype<T1, T2>와 충돌한다.
internal sealed class AType<T3, T4> {}
~~~

가상 제네릭 메서드를 재정의하려면, 재정의하는 메서드는 기본 클래스에서 선언한 메서드와 반드시 동일한 개수의 타입 매개변수를 사용해야 하며, 이 타입 매개변수들에 지정한 제약조건도 일치해야 할 것이다. 하지만 타입 매개변수의 이름을 바꿀 수는 있다.
~~~
internal class Base {
    public virtual void M<T1, T2>()
        where T1 : struct
        where T2 : class {

    }
}

internal class Derived : Base {
    public virtual void M<T3, T4>()
        where T1 : EventArgs    // 오류
        where T2 : class        // 오류
        {}
}
~~~

### 기본 제약조건
타입 매개변수에는 기본 제약조건을 지정하지 않거나 단 한개만 지정할 수 있다. 기본 제약조건으로는 sealed되지 않은 클래스로 참조 타입을 지정할 수 있다.

기본제약 조건으로는 특별히 class와 struct라는 것을 사용할 수 있다. class 제약조건은 컴파일러에 해당 타입 인자로 반드시 참조 타입만을 지정할 수 있음을 알린다. 모든 클래스 타입, 인터페이스 타입, 델리게이트 타입, 배열 타입 등이 이 조건에 부합한다.

다음은 타입 매개변수의 기본 제약조건으로 struct를 설정한 코드의 예다
~~~
internal sealed class primaryConstraintOfStruct<T> where T : struct {
    public static T Factory() {
        return new T();
    }
}
~~~
이 예제에서 T에 대한 새로운 인스턴스를 만드는 것이 가능한 이유는 타입 매개변수 T가 이미 값타입이고, 모든 값 타입은 암묵적으로 매개변수가 없는 public 생성자를 가지고 있기 때문이다. 만약 T에 특별한 제약조건이 설정되어 있지 않았다면 기본적으로 참조 타입이면서 동시에 class 제약조건으로 설정된 것으로 간주하기 때문에 이 코드는 컴파일되지 않는다. 그 이유는 모든 참조타입이 매개변수가 없는 public 생성자를 제공할 것이라고 보장할 수 없기 때문이다.

### 확장 제약조건
타입 매개변수에는 확장 제약조건을 지정하지 않거나 하나 이상 지정할 수 있으며, 인터페이스 타입으로 제약사항을 지정하기 위해서 사용된다. 컴파일러는 타입 인자가 지정된 인터페이스를 구현하고 있는지 검사한다.

인터페이스 제약조건 외에 **타입 매개변수 제약조건**(Type Parameter Constraint)(가끔 노출된 타입 제약조건(Naked Type Constraint)이라고 불리기도 한다)이라는 확장 제약조건이 있는데 인터페이스 제약조건보다는 덜 사용되는 편이다. 이 제약조건을 사용하면 제네릭 타입이나 제네릭 메서드가 취하는 타입 인자들 간의 관계를 지정할 수 있다. 타입 매개변수 제약조건은 타입 매개변수 별로 하나도 지정하지 않거나, 하나 이상 지정할 수 있다.
~~~
private static List<TBase> ConvertIList<T, TBase>(IList<T> list) where T : TBase {
    List<TBase> baseList = new List<TBase>(list.Count);
    for (Int32 index = 0; index < list.Count; index++;) {
        baseList.Add(list[index]);
    }
    return baseList;
}

private static void CallingConvertIList() {
    IList<String> Is = new List<String>();
    ls.Add("A String");

    IList<Object> Io = ConvertIList<String, Object>(ls);

    IList<IComparable> Ic = ConvertIList<String, IComparable>(ls);

    IList<IComparable<String>> Ics = ConvertIList<String, IComparable<String>>(ls);

    IList<String> Is2 = ConvertIList<String, String>(ls);

    IList<Exception> Ie = ConvertIList<String, Exception>(ls); // 오류
}
~~~

### 생성자 제약조건
타입 매개변수에는 생성자 제약 조건을 지정하지 않거나 단 한개만 지정할 수 있다. 생성자 제약조건을 지정하면, 컴파일러는 지정된 타입 인자가 추상 타입이 아니면서 동시에 매개변수가 없는 기본 public 생성자를 정의하고 있는지를 검사하게 된다. 주의해야 할 것은 이 제약조건은 struct 제약조건과 같이 사용할 경우 컴파일러는 이를 오류로 간주하는데, 구조체의 경우 이미 기본 생성자를 이미 가지고 잇으므로 중복되는 것으로 보기 때문이다.

### 기타 검증 가능성 관련 문제
#### 제네릭 타입 변수의 캐스팅
제네릭 타입 변수를 다른 타입으로 캐스팅하는 것은 제약조건에서 지정하는 타입과 호환성이 있는 타입으로 캐스팅하는 것이 아니면 오류로 간주된다.
~~~
private static void CastingAGenericTypeVariable1<T>(T obj) {
    Int32 x = (Int32) obj;      // 오류
    String s  = (String) obj;   // 오류
}

private static void CastingAGenericTypeVariable2<T>(T obj) {
    Int32 x = (Int32)(Object) obj;      // 컴파일 오류 없음
    String s  = (String)(Object) obj;   // 컴파일 오류 없음
}

private static void CastingAGenericTypeVariable3<T>(T obj) {
    String s = obj as String    // 컴파일 오류 없음
}
~~~
#### 제네릭 타입 변수에 기본값을 설정하는 방법
~~~
private static void SettingAGenericTypeVariableToDefaultValue<T>() {
    T temp = default(T);
}
~~~
이와 같이 default 키워드를 사용하면 C# 컴파일러와 CLR의 JIT 컴파일러에 T가 만약 참조 타입인 경우네는 null을, T가 값 타입인 경우 모든 비트가 0으로 설정된 값 타입의 인스턴스를 지정하도록 요청하게 된다.
#### 두 대의 제네릭 타입 변수 간 비교
두개의 동일한 타입의 제네릭 변수를 비교하는 것은 해당 제네릭 타입들을 참조 타입으로 제약하지 않는 이상 컴파일 오류를 발생시킨다.
~~~
private static void ComparingTwoGenericTypeVariables<T>(T o1, T o2) {
    if (o1 == o2) { }   // 오류
}
~~~
앞의 예제에서, T에는 제약조건이 지정되지 않았으며, 참조 타입이라면 두 개의 변수에 대한 == 연산자 비교가 허용되지만, == 연산자를 오버로드 하지 않은 값 타입이라면 문제가 발생하기 때문이다.

