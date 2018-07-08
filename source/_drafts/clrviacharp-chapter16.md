---
title: clrviacharp-chapter16
tags:
---
배열은 여러 개의 항목을 단일 컬렉션으로 다룰 수 있도록 해주는 메커니즘이다. .NET 공용 언어 런타임(CLR)에서는 1차원 배열, 다차원 배열, 중첩 배열(Jagged Array)을 지원한다. 모든 배열 타입은 암묵적으로 System.Array 추상 클래스로부터 상속을 받으며 이 타입은 또 사ㅣ System.Object로부터 상속을 받는다. 모든 배열들은 참조 타입이며, 모든 배열 인스턴스들은 관리되는 힙 메모리 공간에 할당되고, 응용프로그램의 변수 또는 필드에 배열에 대한 참조가 저장되며 배열의 각 요소들이 직접 할당되는 것은 아니다.

### 배열 요소 초기화하기
~~~
String[] names = new String[] { "Aidan", "Grant" };
~~~
중괄호 안쪽에 쉼표로 구분된 토큰들의 집합을 **배열 이니셜라이저**(Array Initializer)라고 부른다.

메서드 내부의 지역 변수를 정의하면서 배열 이니셜라이저를 사용하려는 경우, C#의 암묵적 타입의 지역 변수(var) 기능을 이용하여 다음과 같이 코드를 단순화하는 것도 가능하다.
~~~
var names = new String[] { "Aidan", "Grant" };
~~~

암묵적 타입의 배열을 익명 타입과 암묵적 타입의 지역 변수와 함게 사용하는 사례
~~~
var kids = new[] { new Name = "Aidan" }, new { Name = "Grand } };
foreach (var kid in kids)
    Console.WriteLine(kid.Name);
~~~

### 배열 캐스팅하기
참조 타입 요소로 구성된 배열의 경우, CLR은 암묵적으로 원본 배열의 요소를 특정 타입으로 캐스팅할 수 있다. 이 경우 양쪽의 배열 타입은 반드시 동일한 차수를 지녀야 하고, 원본 요소 타입에서 대상 요소 타입으로 암묵적이든 명시적이든 변환이 가능해야 한다. 하지만 값 타입의 요소로 구성된 배열은 다른 타입으로 캐스팅할 수 없다. (그러나 Array.Copy 메서드를 사용하여, 새로운 배열을 만들고 원래의 배열 요소를 꺼내어 대상 배열로 수동으로 지정할 수는 있을 것이다.)
~~~
// 2차원 FileStream 배열을 생성한다.
FileStream[,] fs2dim = new FileStream[5, 10]

// 2차원 객체 배열을 암묵적으로 캐스팅한다.
Object[,] o2dim = fs2dim;

// 2차원 배열을 1차원 배열로 캐스팅할 수 없다.
Stream[] s1dim = (Sream[]) o2dim;

// 2차원 Stream 배열로 명시적 캐스팅을 할 수 있다.
Stream[,] s2dim (Stream[,]) o2dim;

// 2차원 문자열 배열로 명시적 캐스팅을 할수 있다.
// 컴파일은 이상이 없지만 실행 시 InvalidCastException 예외가 발생한다.
String[,] st2dim = (String[,]) o2dim;

// 1차원 Int32 배열을 새로 만든다.
Int32[] i1dim = new Int32[5];

// 값 타입의 배열에서는 어디로도 캐스팅할 수 없다.
Object[] o1dim = (Object) i1dim;

// 배열을 새로 만들고, Array.Copy 메서드를 이용하여
// 각 요소를 강제로 원본 배열에서 특정 타입으로 구성된 배열로 강테 타입 변환을 수행하여 복사할 수 있다.
// 다음의 코드는 박싱된 Int32 타입의 객체 참조들로 구성된 배열을 만들게 된다.
Object[] ob1dim = new Object[i1dim.Length];
Array.Copy(i1dim, ob1dim, i1dim.Length);
~~~

간혹 배열을 특정 타입에서 다른 타입으로 변환하는 것이 매우 유용할 때가 있다 이러한 기능을 **배열 공변성**(Array Convariance)이라고 부르는데, 이를 활용하면 편리하긴 하지만 그에 대비되는 성능상의 불이익이 있으므로 이를 반드시 이해하고 있어야 한다.
~~~
String[] sa = new String[100];
Object[] oa = sa;
oa[5] = "Jeff";     // 성능저하: CLR이 oa의 요소가 String인지 확인한다. 문제 없다.
oa[3] = 5;          // 성능저하: CLR이 oa의 요소가 Int32 타입인지 확인한다.
                    // ArrayTypeMismatchException 예외가 발생한다.
~~~

### 배열의 전달과 반환
메서드의 매개변수로 배열을 전달할 때에는 실제로는 배열의 참조를 전달하게 된다. 따라서 호출되는 메서드는 내부에서 배열의 요소를 수정할 수 있따. 이를 방지하려면, 반드시 배열의 사본을 만든 후 그 사본을 메서드에 전달해야 한다. 여기서 주의해야 할 것은 Array.Copy 메서드는 얕은 복사(shallow copy)f를 수행하므로, 만약 배열의 요소가 참조 타입인 경우 새로운 배열 사본을 만들었다고 하더라도 여전히 옛 배열 요소를 참조하게 된다.

만약 배열에 대한 참조를 반환하는 메서드를 정의하는 경우, 이 배열이 원소를 하나도 가지지 않는다면 null을 반환하거나 빈 배열을 반환할 수 있을 것이다. 마이크로소프트는 이러한 종류의 메서드를 만들 때 null을 반환하기 보다는 빈 배열을 반환할 것을 강력하게 권고하고 있는데 왜냐하면 이렇게 해야 개발자가 해당 메서드를 호출했을 때 발생할 수 있는 예외에 대한 처리 코드를 최소화할 수 있기 때문이다.

### 배열의 내부 구조
내부적으로 CLR은 두 가지 유형의 배열을 지원한다.
* 1차원 배열이면서 시작 인덱스가 0인 배열을 지원한다. 이러한 배열을 가끔 SZ배열(S는 1차원을, Z는 0부터 인덱스가 시작한다는 규칙을 포함한다) 또는 벡터라고도 한다.
* 시작 인덱스가 지정되지 않은 1차원 배열과 다차원 배열을 지정한다.

실제로 서로 다ㅓ른 종류의 배열들을 다음의 코드를 실행하여 확인해볼 수 있다.
~~~
using System;

public sealed class Program {
    public static void Main() {
        Array a;

        // 1차원이면서 시작 인덱스가 0이고 원소가 없는 배열을 생성한다.
        a = new String[0];
        Console.WriteLine(a.GetType());     // "System.String[]"

        // 1차원이면서 시작 인데스가 0이고 원소가 없는 배열을 생성한다.
        a = Array.CreateInstance(typeof(String), new Int32[] { 0 }, new Int32[] { 0 });
        Console.WriteLine(a.GetType());     // "System.String[]"

        // 1차원이면서 시작 인데스가 1이고 원소가 없는 배열을 생성한다.
        a = Array.CreateInstance(typeof(String), new Int32[] { 0 }, new Int32[] { 1 });
        Console.WriteLine(a.GetType());     // "System.String[*]"

        Console.WriteLine();

        // 2차원이면서 시작 인데스가 0이고 원소가 없는 배열을 생성한다.
        a = new String[0, 0];
        Console.WriteLine(a.GetType());     // "System.String[,]"

        // 2차원이면서 시작 인데스가 0이고 원소가 없는 배열을 생성한다.
        a = Array.CreateInstance(typeof(String), new Int32[] { 0, 0 }, new Int32[] { 0, 0 });
        Console.WriteLine(a.GetType());     // "System.String[,]"

        // 2차원이면서 시작 인데스가 1이고 원소가 없는 배열을 생성한다.
        a = Array.CreateInstance(typeof(String), new Int32[] { 0, 0 }, new Int32[] { 1, 1 });
        Console.WriteLine(a.GetType());     // "System.String[,]"
    }
}