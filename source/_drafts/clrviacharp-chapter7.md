---
layout: dreft
title: clrviacharp-chapter7
date: 2018-07-09 17:31:29
tags:
---
## 상수
**상수**라 함은 절대 불변의 값에 대한 기호다. 상수 기호를 정의할 때에는 그 값이 반드시 컴파일 시점에서부터 알 수 있는 값이어야 한다. 컴파일러는 그 다음 상수의 값을 어셈블리의 메타데이터에 저장한다.

상수 값은 절대로 변하지 않기 때문에, 상수 필드는 언제나 타입의 일부로 취급된다. 달리 표현하면, 상수 값은 항상 인스턴스 멤버가 아닌 정적 멤버로서 취급된다. 상수 값을 선언하는 것은 메타데이터를 새로 만드는 것과 같다.

상수 기호를 참조하는 코드가 있을 때, 컴파일러는 어셈블리의 메타데이터 안에 정의된 상수 기호를 검색하여 상수의 실제 값을 추출한 후 만들어진 IL 코드에 값을 직접 첨부한다. 코드에 상수 값이 직접 첨부되기 때문에, 실행 시점에 상수 값을 위해서 추가로 필요로 하는 메모리 공간이 따로 없다. 더 나아가서 상수값에 대한 주로를 얻거나 이를 참조로 지정할 수 없다. 더 나아가서 상수 값에 대한 주소를 얻거나 이를 참조로 지정할 수 없다. 그리고 이러한 제약사항 덕분에 상수 값에 관해서는 어셈블리 사이에는 참조 관계가 발생하지 않으므로 어셈블리가 변경된다고 할지라도 서로 영향을 받을 개연성이 전혀 얻기 때문에, 절대로 변하지 않을 것이라는 확실할 수 있는 값에 대해서만 상수를 사용하는 것이 바람직한다. 
~~~
using System;

public sealed class someLibraryType {
    // 참고: C#은 상수 필드에 static 키워드를 붙일 수 없도록 하는데 상수 값이 정적 멤버로 간주되기 때문이다.
    public const Int32 MaxEntriesInList = 50;
}
~~~
그리고 이 어셈블리를 다음의 코드와 같이 응용프로그램 내에서 사용할 수 있다.
~~~
using System;

public sealed class Program {
    public static void Main() {
        Console.WriteLine("리스트에서 지원 가능한 항목의 최대 개수: " + SomeLibraryType.MaxEntriesInList);
    }
}
~~~
이 예제에서 버전 관리 문제가 발생함을 알 수 있다. 만약 개발자가 MaxEntriesInList 상수 값을 1000으로 수정하여 해당 DLL만을 다시 빌드한다면, 컴파일이 이루어지지 않은 응용프로그램 어셈블리의 값은 영향을 받지 않게 된다. 응용프로그램 측에서 상수의 새로운 값을 받아들이기 위해서는 반드시 같이 다시 컴파일이 이루어져야만 한다. 만약 어셈블리 사이에 값을 공유하기 위한 목적으로 필드를 선언하기 위함이라면 상수필드는 사용할 수 없다. 대신 readonly 한정자를 사용하여 읽기 전용 필드를 사용할 수 있다.

## 필드
**필드**는 값 타입 인스턴스 또는 참조 타입에 대한 참조를 저장하는 데이터 멤버다.

정적 필드의 경우, 타입 객체의 내부에 필드의 데이터를 할당할 때 동적 메모리를 필요로 하게 되며, 앱도메인안에 타입을 로드할 때 만들어지게 되고, 또 이러한 작업은 해당 타입을 참조하는 메서드가 JIT 컴파일 과정에서 한 개라도 존재할 때 일어나는 일이다. 인스턴스 필드의 경우 타입을 이용한 인스턴스를 생성했을 때 인스턴스 객체의 내부에 필드의 데이터를 할당하는 과정에서 동적 메모리를 필요로 하게 된다.

필드들이 동적 메모리 안에 저장되기 때문에, 필드의 값은 실행 시점에서만 얻어올 수 있다. 필드는 상수 필드를 다룰 때 발생하는 버전 관리의 문제를 해결한다. 더 나아가서 필드는 어떤 데이터 타입이라도 저장 가능하므로, 컴파일러의 내장된 기본 타입에 제약을 받을 필요가 없다.

다음의 예제에서는 readonly 정적 필드뿐만 아니라 readonly 인스턴스 필드, 일반 정적 필드와 일반 인스턴스 필드들을 어떻게 정의할 수 있는지 보여준다.
~~~
public sealed class SomeType {
    // 이것은 읽기 전용 정적 필드이며 클래스를 실행 시점에 최초로 로드할 때
    // 발생하는 초기화 과정에서 값이 계산되고 저장된다.
    public static readonly Random s_random = new Random();

    // 이 필드는 정적 필드이고 읽기 쓰기가 모두 가능하다.
    private static Int32 s_numberOfWrites = 0;

    // 이 필드는 인스턴스 필드이고 읽기 전용앋.
    public readonly String Pathname = "Untitled";

    // 이 필드는 인스턴스 필드이고 읽기 쓰기가 모두 가능한다.
    public System.IO.FileStream m_fs;

    public SomeType(String pathname) {
        // 이 코드는 읽기 전용 필드를 변경한다.
        // 필드에 대한 대입이 생성자 안에서 이루어지므로 이 코드는 문제가 없다
        this.Pathname = pathname;
    }

    public String DoSomething() {
        // 다음 코드는 읽기/쓰기가 허용된 정적 필드에 대해 읽기/쓰기 작업을 수행한다.
        s_numberOfWriters = s_numberOfWrites + 1;
        
        // 다음 코드는 읽기 전용 필드에서 읽기를 수행한다.
        return Pathname;
    }
}
~~~

> **중요**: 참조 타입으로 정의된 필드를 readonly로 선언할 경우, 해당 참조는 다른 참조로 변경할 수 없는 상태가 된다. 그러나 참조를 바꿀 수 없을 뿐 참조가 가리키는 대상을 바꿀 수 없는 것은 아니다. 다음의 코드에서 그 예시를 들고 있다.
~~~
public sealed class Atype {
    public static readonly Char[] InvalidChars = new Char[] { 'A', 'B', 'C' };
}

public sealed class AnothoerType {
    public static void M() {
        // 다음의 코드는 이상이 없으며, 컴파일과 실행 시에도 문제가 없으며,
        // InvalidChars 배열상의 문자를 정상적으로 바꿀 것이다.
        AType.InvalidChars[0] = 'X';
        AType.InvalidChars[1] = 'Y';
        AType.InvalidChars[2] = 'Z';

        // 다음의 코드는 잘못되었으며, 컴파일에 실패할 것이다.
        // InvalidChars를 다른 참조로 변경할 수 없기 때문이다.
        AType.InvalidChars = new Char[] { 'A', 'B'. 'C' };
    }
}
~~~