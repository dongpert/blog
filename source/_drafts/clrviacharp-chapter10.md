---
title: clviacharp-chapter10
tags:
---
## 매개변수가 없는 속성
데이터 캡슐화란 여러분의 타입에서 정의하는 필드가 외부에 드러나지 않도록 하여 필드의 상태를 올바르지 않게 변경하여 상태를 훼손하지 않도록 보호하는 것을 말한다. 이뿐만 아니라 타입의 데이터 필드를 보호하기 위해 캡슐화를 적용해야 할 또 다른 이유도 있다. 필드에 접근하는 과정에서 추가적인 작업을 필요로 하거나, 값을 캐시에 보관하거나, 내부 객체를 지연 생성해야 하는 등의 일이 필요할 수도 있으며 특정 필드에 대하여 스레드-안정적으로 접근해야 할 수도 있다.

타입 내에 정의되어 있는 private 필드를 다루기 위하여 속성의 get 메서드와 set 메서드를 사용하는 것은 일반적인 용례다. 이러한 유형의 필드를 **지원 필드**(Backing Field)라고 부른다.

### 자동 구현 속성
속성을 지원 필드를 캡슐화할 목적으로만 사용하는 경우를 위해서, C#은 **자동 구현 속성**(Automatically Implemented Property, AIP)이라고 불리는 단순화된 문법을 제공한다.
~~~
public sealed class Employee {
    // 이 속성은 자동 구현 속성이다.
    public String Name { get; set; }

    private Int32 m_Age;

    public Int32 Age {
        get { return(m_Age); }
        set {
            if (value < 0)
                throw new ArgumentOutOfRangeException("value", value.ToString(), "The value must be greater than or equal to 0");
            m_Age = value;
        }
    }
}
~~~
개인적으로 다음의 몇 가지 이유 때문에 컴파일러의 자동 구현 속성을 선호하지 않는 편이다.
* 값을 초기화할 수 있는 방법이 없다.
* 코드를 컴파일할 때마다 지원 필드의 이름이 바뀌게 되어 자동 구현 속성을 정의하는 타입을 serialize하면 스트림으로부터 복원할 수 없게 된다.
* get과 set 메서드에 중단점을 추가할 수 없다.

### 속성을 똑똑하게 정의하기
* 속성은 읽기 전용이거나 쓰기 전용일 수 있지만 필드에 대한 접근은 항상 읽기와 쓰기가 모두 가능하다. 만약 속성을 정의한다면 get과 set 접근자 메서드를 모두 정의하는 것이 좋다.
* 속성 메서드는 예외를 발생시킬 수 있지만 필드에 대한 접근은 절대 그럴 일이 없다.
* 속성을 메서드에 out이나 ref 매개변수로 전달할 수는 없지만 필드로는 그렇게 할 수 있다. 다음의 예제는 이러한 이유로 컴파일되지 않는다.
~~~
using System;

public sealed class SomeType {
    private static String Name {
        get { return null; }
        set {}
    }

    static void MathodWithOutParam(out String n) { n = null; }

    public static void Main() {
        // 다음의 코드를 컴파일하려고 하면 C#에서는
        // 속성 또는 인덱서는 out 또는 ref 매개변수로 전달할 수 없습니다.
        MethodWithOutParam(out Name);
    }
}
~~~
* 속성 메서드는 실행하는 데 시간이 오래 걸릴 수 있지만 필드에 대한 접근은 언제나 신속하게 실행된다. 스레드 동기화를 위해 속성을 사용하는 것은 아주 일반적인 사용 예인데, 이 경우 스레드가 영원이 중단될 수도 있다. 따라서 스레드 동기화가 필요한 경우라면 속성을 쓰지 말아야 하며 이런 상황에서는 메서드가 더 적절한 선택이다.
* 만약 속성을 한 줄에서 여러 번 호출하면, 속성 메서드는 매번 다른 값을 반환하지만, 필드는 항상 같은 값을 반환한다.
* 속성 메서드는 일련의 부작용을 일으킬 수 있지만 필드는 그럴 일이 없다.
* 속성의 접근자 메서드는 객체의 상태와는 무관하게 추가적인 메모리를 필요로 하거나 속성을 포함하는 객체의 상태와는 무관한 다른 객체를 반환할 수도 있다.

### 객체와 컬렉션 이니셜라이저
객체를 생성한 다음 객체의 public 필드나 속성에 값을 대입하는 작업은 지극히 일상적인 작업이다. 이러한 프로그래밍 패턴을 단순화하기 위하여, C#에서는 특별한 객체 초기화 문법을 지원한다.
~~~
Employee e = new Employee() { Name = "Jeff", Age = 45 };

String s = new Employee() { Name = "Jeff", Age = 45 }.ToString().ToUpper();

String s = new Employee { Name = "Jeff", Age = 45 }.ToString().ToUpper();
~~~

만약 속성의 타입이 IEnumerable 또는 IEnumerable<T> 인터페이스를 구현하는 타입인 경우, 속성은 컬렉션으로 취급되고, 속성에 대한 초기화 문법은 속성 값의 변경이 아니라 컬렉션에 대한 항목 추가로 의미가 변경된다.
~~~
public sealed class Classroom {
    private List<String> m_students = new List<String>();
    public List<String> Students { get { return m_student; } }

    public Classroom() {}
}

public static void M() {
    Classroom classroom = new Classroom {
        Students = { "Jeff", "Kristin", "Aidan", "Grant" }
    };

    foreach (var studend in classroom.Students)
        Console.WriteLine(student);
}
~~~

### 익명타입
C#의 익명 타입 기능을 이용하면 변경 불가능한 튜플 타입을 매우 단순하고 명료한 문법을 사용하여 자동으로 선언할 수 있다. **튜플 타입**은 상호간에 연관성이 있는 속성들의 컬렉션을 포함하는 타입이다.
~~~
var o1 = new { Name = "Jeff", Year = 1964 }:
Console.WriteLine("Name={0}, Year={1}", o1.Name, o1.Year);
~~~
컴파일러는 각각의 표현식으로부터 정확한 타입을 유추하여 private 읽기 전용 필드를 만들고, 각 필드에 대한 읽기 전용 속성을 정의하며, 주어진 값들을 받아들일 수 있도록 생성자를 만든다. 생성자의 코드는 읽기 전용의 private 필드들을 초기화하도록 자동으로 만들어지게 된다. 뿐만아니라 컴파일러는 Object 타입의 Equals, GetHashCode, ToString 메서드까지 자동으로 재정의하고 각각의 메서드에 대한 구현부를 자동으로 구성한다.

익명 타입은 LINQ와 함께 가장 많이 사용되는데, 쿼리의 결과가 단일 익명 타입의 인스턴스들로 구성된 컬렉션으로 작성되며, 그 다음으로 컬렉션의 객체를 각각 처리할 수 있다. 이러한 모든 동작이 단일 메서드 내에서 수행된다.
~~~
String myDocuments = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
var query = from pathname in Directory.GetFiles(myDocument);
            let LastWriteTime = File.GetLastWriteTime(pathname)
            where LastWriteTime > (DateTime.Now - TimeSpan.FromDays(7))
            orderby LastWriteTime
            select new { Path = pathname, LastWriteTime };

foreach (var file in query)
    Console.WriteLine("LastWriteTime={0}, Path={1}", file.LastWriteTime, file.Path);
~~~
익명 타입의 인스턴스들은 메서드 바깥으로 가져갈 수 없으며, 익명 타입의 인스턴스를 취하는 메서드를 정의할 수도 없다. 왜냐하면 익명 타입의 이름이 무엇인지 알 수 없기 때문이다. 그리고 비슷한 이유로 익명 타입의 참조를 반환하도록 메서드를 정의할 수도 없다.비록 익명 타입들 조차도 Object 타입을 상속하기 때문에 Object 타입으로 캐스팅할 수는 있지만, 컴파일 시점에는 익명 타입의 이름을 알 수 없으므로, Object 타입을 원래의 타입으로 되돌릴 방법이 없다.

### System.Tuple 타입
마이크로소프트는 System 네임스페이스 안에 제네릭 타입 매개변수의 개수를 달리하는 몇가지 종류의 Tuple 타입들을 정의해두었다.

익명 타입과 마찬가지로 Tuple 타입의 인스턴스는 생성된 이후 변경이 불가능하도록 모든 속성들을 읽기 전용으로 구성해 두었으며, 익명 타입과 마찬가지로 CompareTo, Equals, GetHashCode, ToString 메서드를 구현하고 있으며, 별도로 Size 속성도 제공한다. 더 나아가서 Tuple 타입은 IStructuralEquatable, IStructuralComparable, IComparable 인터페이스를 구현하고 있어서 두 개의 Tuple 객체를 각 필드별로 비교할 수 있다.

다음 예제 코드는 호출자에 두 가지 정보를 반환하기 위해서 Tuple 타입을 사용한 메서드의 예다.
~~~
// 최소값을 Item1 속성에, 최대값을 item2 속성에 대입하여 반환한다.
private static Tuple<Int32, Int32> MinMax(Int32 a, Int32 b) {
    return new Tuple<Int32, Int32>(Math.Min(a, b), Math.Max(a, b));
}

// 메서드를 호출하고 반환된 튜플을 어떻게 사용하는지 보여준다.
private static void TupleTypes() {
    var minmax = MinMax(6, 2);
    Console.WriteLine("Min={0}, Max{1}", minmax.Item1, minmax.Item2);
}
~~~
컴파일러는 제네릭 메서드를 호출할 때에만 제네릭 타입을 유추할 수 있으며, 생성자를 호출할 때는 그렇지 않다. 이런 이유로, System 네임스페이스에는 매개변수로 제네릭 타입을 유추할 수 있는 여러 개의 정적 Create 메서드를 포함하고 있다. 이 클래스는 Tuple 객체를 생성하는 팩토리처럼 동작하는데, 단순히 코드를 단순화하기 위해서 존재하는 것뿐이다. 다음에 정적 Tuple 클래스를 이용하여 앞서 보여준 MinMax 예제를 다시 작성하였다.
~~~
private static Tuple<Int32, Int32> MinMax(Int32 a, Int32 b) {
    return Tuple.Create(Math.Min(a, b), Math.Max(a, b));    // 더 단순한 문법
}
~~~

### 매개변수가 있는 속성
C#에서는 매개변수가 있는 속성(인덱서)을 마치 배열과 같은 문법으로 사용할 수 있도록 해주고 있다. 달리 표현하면, 인덱서는 C# 개발자가 [] 연산자를 오버로딩하는 또 다른 방법으로 생각할 수 있다.
~~~
using System;

public sealed class BitArray {
    private Byte[] m_byteArray;
    private Int32 m_numBits;

    public BitArray(Int numBits) {
        if (numBits <= 0)
            throw new ArgumentOutOfRangeException("numBits must be > 0");

        n_numBits = numBits;

        m_byteArray = new Byte[(numBits + 7) / 8];
    }

    // 이 멤버가 인덱서, 즉 매개변수가 있는 속성이다.
    public Boolean this[Int32 bitPos] {
        get {
            if ((bitPos < 0) || bitPos >= m_numBits))
                throw new ArgumentOutOfRangeException("bitPos");

            return (m_byteArray[bitPos / 8] & (1 << (bitPos % 8))) != 0;
        }

        set {
            if ((bitPos < 0) || bitPos >= m_numBits))
                throw new ArgumentOutOfRangeException("bitPos", bitPos.ToString());

            if (value) {
                m_byteArray[bitPos / 8] = (Byte)(m_byteArray)[bitPos / 8] | (1 << (bitPos % 8 )));
            } else {
                m_byteArray[bitPos / 8] = (Byte)(m_byteArray)[bitPos / 8] & ~(1 << (bitPos % 8 )));
            }
        }
    }
}

BitArray ba = new BitArray(14);

for (Int32 x = 0; x < 14; x++) {
    ba[x] = (x % 2 == 0);
}

for (Int32 x = 0; x < 14; x++) {
    Console.WriteLine("Bit " + x + " is " + (ba[x] ? "On" : "Off"));
}
~~~

### 속성 접근자의 한정자
어떤 타입을 설계할 때에는 get 접근자 메서드와 set 접근자 메서드의 접근성을 서로 다르게 설정해야 할 경우가 간혹 있다.
~~~
public class SomeType {
    private String name;
    public String Name {
        get { return m_name; }
        protected set { m_name = value; }
    }
}
~~~