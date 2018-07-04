---
title: clrviacharp-chapter4
tags:
---
### 모든 타입은 System.Object를 상속한다.
CLR은 모든 객체들을 반드시 new 연산자에 의하여 만들도록 하고 있다.
~~~
Employee e = new Employee("ConstructorParam1");
~~~

여기서 new 연산자가 하는 일들을 간단히 정리하면 다음과 같다.
1. 할당하려는 타입과 별도의 인스턴스가 없는 System.Object 타입을 포함한 그 위의 모든 기본 타입들에서 정의된 모든 인스턴스 필드들을 메모리에 할당하기 위한 바이트 수를 계산한다. 힙상의 모든 객체에는 별도의 추가적인 멤버로, 타입 객체 포인터(Type Object Pointer)와 동기화 블록 인덱스(Sync Block Index)가 추가되며 CLR에 의해 객체를 관리하기 위하여 사용된다.
2. 지정된 타입의 할당에 필요한 바이트의 수만큼 관리되는 힙으로부터 객체를 위하여 메모리를 할당하며, 처음 할당할 때에는 모든 바이트를 0으로 초기화 한다.
3. 객체의 타입 객체 포인터와 동기화 블록 인덱스 멤버를 초기화한다.
4. 클래스 타입의 인스턴스 생성자와 인수가 new 연산자에서 서술한 대로 전달된다. 각각의 호출되는 생성자의 타입에 의하여 정의된 인스턴스 필드들을 초기화해야 한다. 호출되는 생성자는 상속 관계에 따라 거슬러 올라가 종국에는 System.Object의 생성자를 부르게 되며, 이 생성자는 하는 일 없이 반환된다.

### 타입 간 캐스팅하기
CLR의 중요한 기능들 중 하나는 타입 안정성이다. 실행 시점에서 CLR은 객체의 정확한 타입이 무엇인지 항사 파악하고 있다. 객체의 정확한 타입을 알아내기 위하여 GetType 메서드를 언제든 호출할 수 있다. 이 메서드가 비가상 메서드이기 때문에, 다른 타입에 대한 정보를 대신 반환하는 것은 불가능하다.

### C#의 is와 as 연산자로 캐스팅하기
C# 언어에서 캐스팅 연산을 다루는 또 다른 방법은 is 연산자를 사용하는 것이다. C#의 is 연산자는 어떤 객체가 주어진 타입과의 호환성이 있는지 여부를 판정하여, 참 또는 거짓으로 결과를 반환하는 기능이 있다. 이 연산자는 절대 예외를 발생시키지 않는다.
~~~
Object o = new Object();
Boolean b1 = (o is Object);
Boolean b2 = (o is Employee);
~~~
다음과 같이 is 연산자를 보통 사용한다.
~~~
if (o is Employee) {
    Employee e = (Employee) o;
}
~~~
이 코드에서 CLR은 객체의 타입을 두 번 점검하는데, is 연산자로 o 객체가 Employee 타입과의 호환성이 있는지 확인한다. 만약 그렇다면, if 코드 블록 안의 내용이 실행되어 CLR이 o 객체가 Employee 타입으로 캐스팅하면서 검사를 수행한다. 이러한 프로그래밍 패러다임이 일반적이기는 하지만, C#은 이러한 작업을 단순화하고 성능을 개선할 수 있도록 하기 위하여 as 연산자를 제공한다.
~~~
Employee e = o as Employee;
if (e != null) {

}
~~~
as 연산자는 예외를 전혀 발생시키지 않으면서 캐스팅을 수행하며, 객체를 캐스팅할 수 없는 경우 null을 반환하게 된다. 

지금까지 이야기한 내용을 확실히 이해하기 위해서 아래의 간단한 퀴즈를 풀어보도록 하자. 다음의 두 클래스에 대한 정의가 있다고 가정하겠다.
~~~
internal class B {}

internal class D : B {}
~~~

* 타입 안정성에 대한 퀴즈

| 문장 | 이상 없음 | 컴파일 오류 | 실행 중 오류 |
| :- | :- | :- | :- | :- |
| Object o1 = new Object(); | V | | |
| Object o2 = new B(); | V | | |
| Object o3 = new D(); | V | | |
| Object o4 = o3; | V | | |
| B b1 = new B(); | V | | |
| B b2 = new D(); | V | | |
| D d1 = new D(); | V | | |
| B b3 = new Object(); | | V | |
| D d2 = new Object(); | | V | |
| B b4 = d1; | V | | |
| D d3 = b2; | | V | |
| D d4 = (D) d1; | V | | |
| D d5 = (D) b2; | V | | |
| D d6 = (D) b1; | | | V |
| B b5 = (B) o1; | | | V |
| B d6 = (D) b2; | V | | |

### 네임스페이스와 어셈블리
네임스페이스는 서로 관련이 있는 타입들을 논리적으로 그룹화하기 위한 수단으로, 개발자들은 특정한 타입을 쉽게 찾을 수 있도록 활용하곤 한다.

컴파일러가 이러한 방법으로 네임스페이스를 취급하기 때문에 잠재적인 문제점들이 하나 이상 발생하게 될 것이다. 동일한 이름을 가지는 서로 다른 네임스페이스 상의 타입이 있을 수 있다는 것이다.
~~~
using Microsoft;
using Wintellect;

public sealed class Program {
    public static void Main() {
        Widget w = new Widget();    // 모호한 참조 발생
    }
}
~~~

C#의 using 구문의 한 형태를 이용하면 특정 타입에 대한 별명을 만들 수 있다. 이 방식을 사용하면 특정 네임스페이스 안에 들어있는 모든 네임스페이스를 글로벌 네임스페이스로 병합하지 않고 원하는 타입만을 가져와서 쓸 수 있으므로 매우 유용하다.
~~~
using Microsoft;
using Wintellect;
using WintellectWidget = Wintellect.Widget

public sealed class Program {
    public static void Main() {
        WintellectWidget w = new WintellectWidget();    // 오류를 해결하였다.
    }
}
~~~