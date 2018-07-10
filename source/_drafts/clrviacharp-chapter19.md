---
title: clrviacharp-chapter19
tags:
---
이미 알다시피, 값 타입의 변수에는 절대로 null을 대입할 수 없으며, 반드시 어떤 값을 가져야한다. 값 타입이 왜 "값"에 관련된 타입인가를 설명하는 이유이기도 하다. 안타깝게도 몇몇 시나리오에서는 이러한 특성이 문제가 된다. 예를 들어 데이터베이스를 설계할 때 열의 데이터 타입이 32비트 정수 값이라면 여기에 대응되는 타입으로 프레임워크 클래스 라이브러리의 Int32 타입을 선택할 수 있을 것이다. 하지만 데이터베이스에서는 32비트 정수값이 null을 가질 수도 있다. .Net Framework에서 이러한 데이터베이스를 다룰 때에는 문제가 복잡해지는데, CLR에서는 Int32 값 타이을 사용하면 null 값을 표현할 방법이 없기 때문이다.

이러한 문제점을 개선하기 위하여, 마이크로소프트에서는 null을 허용할 수 있는 값 타입을 CLR에 새로 추가하였다. 이러한 개념이 어떻게 동작하는지 이해하기 위하여, 우선 FCL에 정의된 System.Nullable<T> 구조체에 대해서 살펴봐야 한다.

## C#의 Nullable 값 타입에 대한 지원
C#은 Nullable 값 타입을 지원하기 위해서 좀 더 단순한 문법을 지원한다. 실제로 C#팀에서는 C# 언어 자체에 Nullable 값 타입을 통합하여 자연스럽게 사용할 수 있도록 하고자 했다. 이를 위해 C#에서는 Nullable 값 타입을 사용하기에 매우 편리한 문법을 제공한다. C#에서는 다음과 같이 물음표 기호를 타입 뒤에 붙여 x와 y 변수를 선언하는 것을 허용한다.
~~~
Int32? x = 5;
Int32? y = null;
~~~
C#에서 Int32?는 Nullable<Int32>와 같은 의미다. 게다가 C#은 여기서 한발 더 나아가서 Nullable 값 타입의 인스턴스에 대해 변환과 캐스팅까지도 지원하며, Nullable 값 타입의 인스턴스에 대한 연산자도 지원하고 있다. 다음의 코드를 살펴보자.
~~~
private static void ConversionsAndCasting() {
    // Int32 타입과 Nullable<Int32> 타입 간의 암묵적 변환을 지원한다.
    Int32? a = 5;

    // Nullable<Int32>에 null 참조 대입을 지원한다.
    Int32? b = null;    // "Int32? b = new Int32?();"와 같은 의미로
                        // HasValue 속성이 false로 설정된다.

    // Nullable<Int32> 타입에서 Int32 타입으로 명시적 변환을 지원한다.
    Int32 c = (Int32) a;

    // Nullable 기본 타입 간의 캐스팅을 지원한다.
    Double? d = 5;  // Int32 -> Double?
    Double? e = b;  // Int32? -> Double?
}
~~~
C#은 또한 Nullable 값 타입 인스턴스에 대한 연산자도 지원한다. 다음의 코드를 살펴보자.
~~~
private static void Operators() {
    Int32? a = 5;
    Int32? b = null;

    a++;
    b = -b;

    a = a + 3;
    b = b * 3;

    if (a == null) {} else {}
    if (b == null) {} else {}
    if (a != b) {} else {}

    if (a < b) {} else {}
}
~~~
다음은 C#이 연산자를 어떻게 해석하는지에 대한 규칙을 설명한 것이다.
* 단항 연산자(+, ++, -, --, !, ~): 만약 오퍼랜드가 null이면 결과도 null이다.
* 이항 연산자(+, -, *, /, %, |, ^, <<, >>): 만약 양쪽 오퍼랜드가 모두 null이면 결과도 null이다. 하지만 논리 연산자(&r과 |)에 대해서는 예외적인 규칙이 있는데, SQL에서의 3진 논리(Three-valued Logic)와 같은 규칙을 가진다.
* 동일 비교 연산자(==, !=): 만약 양쪽 오퍼랜드가 모두 null이면 동일한 것으로 간주한다.
* 대소 비교 연산자(<, >, <=, >=): 만약 양쪽 오퍼랜드가 모두 null이면 결과는 false가 되며, 양쪽 오퍼랜드가 모두 null이 아니라면 값의 대소를 비교한다.

## C#의 Null 결합 연산자
C#은 **Null 결합 연산자**(Null-Coalescing Operator)라고 부르는 연산자를 지원하는데 두 개의 오퍼랜드를 필요로 한다.

Null 결합 연산자는 참조 타입뿐만 아니라 Nullable 값 타입에 대해서도 사용할 수 있는 멋진 기능이다. 다음은 Null 결합 연산자를 사용하는 예다.
~~~
private static void NullCoaleacingOperator() {
    Int32? b = null;

    Int32 x = b ?? 123;
    Console.WriteLine(x);

    String filename = GetFilename() ?? "Untitled";
}
~~~
Null 결합 연산자는 두가지 면에서 문법적 발전을 제안하고 있다. Null 결합 연산자는 다음과 같은 표현식에서도 잘 작동한다.
~~~
Func<String> f = () => SomeMethod() ?? "Untitled";
~~~
그리고 Null 결합 연산자는 중첩 사용할 때에도 이점도 있다.
~~~
String s = SomeMethod1() ?? SomeMethod2() ?? "Untitled";
~~~

## CLR의 Nullable 값 타입에 대한 특별한 배려
### Nullable 값 타입에 대한 박싱
CLR이 Nullable<T> 인스턴스를 박싱해야 할 경우, 해당 인스턴스의 상태가 null인지 확인하여, 만약 그렇다면 CLR은 실제로 아무것도 박싱하지 않은 채로 null 참조를 반환한다. 만약 null 상태가 아니라면, CLR은 실제 값을 꺼내서 원래대로 박싱을 수행한다.
### Nullable 값 타입의 언박싱
CLR은 박싱된 값 타입 T를 언박싱하거나 Nullable<T>로 변환하는 것을 허용한다. 만약 박싱된 값 타입의 참조가 null이면 Nullable<T> 타입을 언박싱하려 할 때, null 상태로 설정하게 된다. 다음의 코드에서 이러한 동작의 예를 들고 있다.
~~~
Object o = 5;

Int32 a = (Int32?) o;
Int32 b = (Int32) o;

o = null;

a = (Int32?) o;     // a = null
b = (Int32) o;      // NullReferenceException
~~~
### Nullable 값 타입에 대한 GetType 메서드 호출
NullType<T> 객체는 GetType 메서드를 호출하면 CLR은 해당 객체의 타입이 Nullable<T> 타입이 아니라 T 타입이라고 결과를 바꿔서 돌려준다.
### Nullable 값 타입을 통한 인터페이스 메서드 호출
다음 코드를 살펴보면 Nullable<Int32> 타입의 변수 n을 IComparable<Int32> 인터페이스 타입의 변수로 캐스티앟고 있다. 하지만 Int32 타입은 IComparable<Int32>를 구현하고 있지만 Nullable<T> 타입은 IComparable<Int32> 인터페이스를 구현하고 있지 않다. C# 컴파일러는 그럼에도 불구하고 이러한 코드를 컴파일할 수 있도록 기능을 내장하고 있어서 다음과 같이 코드를 작성할 수 있다.
~~~
Int32? n = 5;
Int32 result = ((IComparable) n).CompareTo(5);  // 컴파일과 실행에 문제가 없다.
Console.WriteLine(result);
~~~