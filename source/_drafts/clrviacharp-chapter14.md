---
title: clrviacharp-chapter14.md
tags:
---
### 문자
.NET Framework에서는 문자를 항상 16비트 유니코드 값으로 표현하기 때문에, 다국적 응용프로그램 개발을 비교적 쉽게 개발할 수 있다. 한 문자는 System.Char 구조체로 표현된다. System.Char 타입은 매우 단순한 구성을 가지고 있따. 두 개의 읽기 전용 상수 필드가 포함되어 있는데, '\0'로 정의되어 있는 MinValue와 '\uffff'로 정의되어 있는 MaxValue 필드가 있다.

다양한 숫자 타입과 Char 타입을 상호 변환할 수 있는 세 가지 방법이 있다.
* 캐스팅: Char 타입을 Int32 타입과 같은 숫자 값으로 변환할 수 있는 가장 쉬운 방법이다. 컴파일러가 이 작업을 직접 수행하는 IL 명령어를 직접 생성하기 때문에 추가적인 메서드 호출이 필요하지 않기 때문에 가장 효율적이다. 또한 C#과 같은 몇몇 언어들은 checked나 unchecked를 이용하여 변환 과정에서 발생할 수 있는 오버플로우의 발생 여부를 검사하거나 검사하지 않을 수 있다.
* Convert 타입 사용: System.Convert 타입에는 Char 타입을 다른 타입으로 변환하거나, 다른 타입을 Char 타입으로 변환하는 다수의 정적 메서드들이 포함되어 있다. 이 메서드들은 모두 오버플로우 검사를 수행하도록 되어있으며, 데이터 소실 가능성이 예상되는 경우 OverflowException 예외를 발생시킨다.
* IConvertible 인터페이스의 사용: Char 타입뿐 아니라 FCL에서 제공하는 모든 수치 타입들은 IConvertible 인터페이스를 구현하다. 이 인터페이스는 ToUInt16이나 ToChar와 같은 메서드들을 정의하고 있다. 값 타입의 인스턴스를 이용하여 인터페이스 메서드를 호출하려면 반드시 참조 타입으로의 박싱이 필요하기 때문에 가장 효율적이지 않다. IConvertible 인터페이스가 정의하고 있는 메서드들은 Char에서 Boolean으로 변환하는 경우처럼 변환할 수 없거나, 데이터 소실 가능성이 있는 요청을 만나면 System.InvalidCastException 예외를 발생시킨다.

다음의 코드는 지금 이야기한 세 가지 방법을 사용하는 예다.
~~~
public static class Program {
    public static void Main() {
        Char c;
        Int32 n;

        c = (Char) 65;
        Console.WriteLine(c);

        n = (Int32) c;
        Console.WriteLine(n);

        c = unchecked((Char) (65536 + 65));
        Console.WriteLine(c);

        c = Convert.ToChar(65);
        Console.WriteLine(c);

        n = Convert.ToInt32(c);
        COnsole.WriteLine(n);

        try {
            c = Convert.ToChar(70000);
            Console.WriteLine(c);
        }
        catch (OverflowException) {
            Console.WriteLine("70000을 Char 타입으로 변환할 수 없다.");
        }

        c = ((IConvertible) 65).ToChar(null);
        Console.WriteLine(c);

        n = ((IConvertible) c).ToInt32(null);
        Console.WriteLine(n);
    }
}
~~~

### System.String 타입
String 타입은 변경할 수 없는 문자들의 순서 열이다. String 타입은 Object 타입을 직접 상속하기 때문에 참조 타입으로 분류되며, 따라서 String 타입과 그 내부의 배열은 항상 힙에 할당되고 절대 스레드의 스택영역에 할당되지 않는다.

#### 문자열 만들기
C#을 포함한 많은 프로그래밍 언어들이 String을 기본 타입으로 간주하는데, 컴파일러가 문자열 리터럴(literal)을 소스 코드상에서 직접 표현할 수 있도록 한다. 컴파일러는 리터럴 문자열을 모듈의 메타데이터 영역에 배치하고, 실행 시점에 이 메타데이터를 메모리에 로드한 후 참조하게 된다.

C#은 소스 코드에 리터럴 문자열을 표현하기 위한 특별한 문법을 지원한다. 줄 바꿈, 캐리지 리턴, 백스페이스 같은 특수문자를 포함하고 있는 문자열을 소스코드에 쉽게 입력하기 위하여 이스케이프 메커니즘을 지원한다.

C#의 + 연산자를 이용하면 다음과 같이 여러 조각의 문자열을 하나의 문자열로 연결할 수 있다.
~~~
String s = "Hi" + " " + "there.";
~~~
이 코드에서는 모든 문자열이 리터럴 문자열이므로 C# 컴파일러는 컴파일 시점에 모든 문자열들을 연결하여 하나의 단일 문자열로 합하여 모듈의 메타데이터 안에 저장한다. + 연산자를 리터럴이 아닌 문자열 인스턴스에 사용하면 실행 시점에 문자열 연결이 수행된다. 여러 문자열을 실행 시에 연결하려고 할 때에는, 가급적 + 연산자를 사용하지 않는 것이 좋은데, 관리 힙상에 여러 개의 임시 문자열을 생성할 가능성이 있기 때문이다.

C#은 문자열을 선언하는 특별한 방법을 하나 더 제공하는데, 이 방법을 이용하면 모든 문자들을 문자열의 일부로 간주한다. 이러한 특별한 문자열 선언을 축자 문자열(Verbatim String)이라고 하며, 보통은 파일이나 디렉터리 경로를 지정하거나 정규표현식을 지정할 때 많이 사용한다.
~~~
String file = "C:\\Windows\\System32\\Notepad.exe";
String file = @"C:\Windows\System32\Notepad.exe";
~~~

#### 문자열 비교하기
두 개의 문자열을 비교하는 이유로는 크게 두 가지가 있는, 하나의 문자열이 동일한지를 판단하기 위함이고 다른 하나는 정렬을 위해서다.

정렬 시에는 항상 대소문자를 구분하도록 비교하는 것이 좋다.

StringComparison은 다음과 같이 정의되어 있다.
~~~
public enum StringComparison {
    CurrentCulture = 0,
    CurrentCultureIgnoreCase = 1,
    InvariantCulture = 2,
    InvariantCultureIgnoreCase = 3,
    Ordinal = 4,
    OrdinalIgnoreCase = 5
}
~~~

CompareOptions는 다음과 같이 정의되어 있다.
~~~
[Flags]
public enum CompareOptions {
    None = 0,
    IgnoreCase = 1,
    IgnoreNonSpace = 2,
    IgnoreSymbols = 4,
    IgnoreKanaType = 8,
    IgnoreWidth = 16,
    OrdinalIgnoreCase = 268435456,
    StringSort = 536870912,
    Ordinal = 1073741824
}
~~~
CompareOption 매개변수를 받는 메서드는 명시적으로 문화권도 설정해줘야 하지만, options 매개변수로 Ordinal이나 OrdinalIgnoreCase 플래그로 지정하게 되면, 매개변수로 전달한 문화권 정보를 무시한다.

프로그램 안에서만 사용하는 문자열을 비교하는 경우라면, 반드시 StringComparison.Ordinal 또는 StringComparison.OrdinalIgnoreCase 옵션을 사용해야 한다. 이 옵션을 사용해야 특정 언어에 대한 특수한 규칙이나 문화권의 특성을 고려하지 않고 빠르게 비교를 수행할 수 있기 때문이다.

반면, 문자열을 비교할 때 언어의 특성을 고려하여 올바른 방식으로 문자열을 비교하려면, 반드시 StringComparison.CurrentCulture 또는 StringComparison.CurrentCultureIgnoreCase 옵션을 사용해야 한다.

> **중요** : 대부분의 경우, StringComparison.InvariantCulture와 StringComparison.InvariantCultureIgnore 옵션을 사용해서는 안된다. ㅍ프로그램 안에서만 사용하는 문자열을 다루는 경우, 비록 이들 메서드들이 언어상으로 정확한 비교를 한다 하더라도, 문자열을 비교하는 작업 치고는 다른 비교 메서드에 비해 시간이 너무 오래 소요된다.

> **중요** : 만약 문자열에 대해서 서수 비교(Ordinal Comparison)를 수행하기 위해, 문자열을 대문자나 소문자로 통일하려고 한다면, 반드시 String의 ToUpperInvariant나 ToLowerInvariant 메서드를 사용해야 한다. 그리고 ToUpper와 ToLower 메서드는 문화권 정보에 영향을 받기 때문에 가급적 사용을 피하는 것이 좋다.

> **중요** : String의 다른 비교 메서드인 CompareTo나 같음(==) 연산자 및 같지 않음(!=) 연산자의 사용을 피해야 한다. 이 메서드들과 연산자의 사용을 지양해야 하는 이유는 호출자가 문자열 비교 방법을 명시적으로 지정할 수 있는 방법이 없고, 이러한 메서드나 연산자의 이름을 통해서 어떤 문자열 비교 방법이 사용될지를 파악할 수 없기 때문이다.

#### 문자열 내의 문자와 텍스트 요소 확인하기
다음의 코드에서는 StringInfo 클래스의 다양한 사용법과 함께 문자열의 텍스트 요소를 활용하는 방법을 보여준다.
~~~
using System;
using System.Text;
using System.Globalization;
using System.Windows.Forms;

public sealed class Program {
    public static void Main() {
        String s = "a\u0304\u0308bc\u0327";
        SubstringByTextElements(s);
        EnumTextElements(s);
        EnumTextElementsIndexes(s);
    }

    private static void SubstringByTextElements(String s) {
        String output = String.Empty;
        StringInfo si = new StringInfo(s);
        for (int element = 0; element < si.LengthInTextElements; element++) {
            output += String.Format("Text element {0} is '{1}'{2}", element, si.SubstringByTextElements(element, 1), Environment.NewLine);
        }
        MessageBox.Show(output, "Result of SubstringBytextElements");
    }

    private static void EnumTextElements(String s) {
        String output = String.Empty;

        TextElementEnumerator charEnum = StringInfo.GetTextElementEnumerator(s);
        while (charEnum.MoveNext()) {
            output += String.Format("Chatacter at index {0} is '{1}'{2}", charEnum.ElementIndex, charEnum.GetTextElement(), Environment.NewLine);
        }
        MessageBox.Show(output, "Result of GetTextElementEnumerator");
    }

    private static void EnumTextElementsIndexes(String s) {
        String output = String.Empty;

        Int32[] textElemIndex = StringInfo.ParseCombiningCharacters(s);
        for (Int32 i = 0; i < textElemIndex.Length; i++) {
            output += String.Format("Character {0} starts at index {1}{2}", i, textElemIndex[i], Environment.NewLine);
        }
        MessageBox.Show(output, "Result of ParseCombiningCharacters");
    }
}
~~~

#### 기타 문자열 작업들
| 멤버 | 메서드유형 | 설명 |
| - | - | - |
| Clone | 인스턴스 | 현재 객체(this)와 동일한 참조를 반환한다. String 객체는 변경할 수 없기 때문에 이러한 동작은 문제가 되지 않는다. |
| Copy | 정적 | 특정 문자열에 대한 복사본을 만든다. 이 메서드는 거의 사용되지 않으며, 응용프로그램에서 문자열을 토큰처럼 사용하는 경우를 위해서 지원된다. 이 메서드는 새로운 문자열 객체를 만들기 때문에 동일 문자로 구성된 문자열이라 하더라도 다른 참조(포인터)를 가지게 된다. |
| CopyTo | 인스턴스 | 문자열 일부분을 문자의 배열로 복사 |
| SubString | 인스턴스 | 원래의 문자열에서 일부분의 문자열을 나타내는 새로운 문자열을 반환한다. |
| ToString | 인스턴스 | 현재 객체(this)의 참조를 그대로 반환한다.

### 효율적으로 문자열 생성하기
String 타입은 변경할 수 없기 때문에, FCL에서는 System.Text.StringBuilder라는 별도의 타입을 만들어, 문자열과 여러 문자들을 이용하여 동적으로 새로운 String 인스턴스를 만드는 방법을 제공하고 있다.

논리적으로, StringBuilder 객체는 Char 타입의 배열을 나타내는 필드 하나를 가지고 있다. StringBuilder의 멤버들을 통해서 이 문자 배열을 다룰 수 있도록 해주며, 문자열 내의 문자들의 개수 변화에 맞게 배열의 크기를 자유롭게 확장하거나 축소할 수 있다.

#### StringBuilder 객체 생성하기
일부 기본 타입처럼 특별한 구문 특성을 가지고 있는 것은 아니므로 다른 일반 타입들과 같이 객체를 생성할 수 있다.
~~~
StringBuilder sb = new StringBuilder();
~~~
* 최대 용량: Int32 타입의 값으로 문자열에 들어갈 수 있는 문자의 최대 개수다. 기본값은 Int32.MaxValue로 약 2억에 가까운 수치이며, 이 값을 바꾸는 경우는 흔하지 않다.
* 용량: 현재 StringBuilder에서 관리하는 문자 배열의 크기를 나타내는 Int32 타입의 값이다. 기본값은 16이다.
* 문자 배열: Char 구조체의 배열로 구성되어 있으며, 문자들의 집합이므로 말 그래도 "문자열"이다.

#### StringBuilder 멤버
String과는 다르게 StringBuilder는 변경 가능한 문자열을 나타내기 위한 객체다. 따라서 StringBuilder 내의 거의 모든 멤버들은 매번 관리 힙상에 새로운 객체를 생성하지 않고, 기존 문자 배열의 내용을 수정하게 된다. 실제로 StringBuilder는 딱 두가지 이유에 의해서만 새로운 객체를 생성한다.
* 초기에 지정한 용량보다 큰 문자열을 동적으로 만들어야 하는 경우
* StringBuilder의 ToString 메서드를 호출하는 경우

StringBuilder의 메서드들에 관해서 한 가지 참고할 중요한 정보는 이들 중 대부분이 동일한 StringBuilder 객체를 다시 반환한다는 것이다. 이 덕분에 문법적으로 다음과 같이 여러 작업을 연결하여 수행할 수 있다.
~~~
StringBuilder sb = new StringBuilder();
String s = sb.AppendFormat("{0} {1}, "Jeffrey", "Richter").Replace(' ', '-').Remove(4, 3).ToString();
Console.WriteLine(s);
~~~

String과 StringBuilder 클래스는 완전히 동일한 메서드들을 가지고 있는 것은 아니다. 이 두 타입이 제공하는 메서드들이 제공하는 메서드들이 기능적으로 완전히 일치하지는 않기 때문에, 특정 작업의 경우 String이나 StringBuilder 타입으로 변경을 수행해야만 한다. 예를 들어 문자열을 구성하는 과정에서 모든 문자들을 대문자로 변환한 후, 문자열을 중간에 삽입하는 코드를 작성하려면 다음과 같이 할 수 밖에 없다.
~~~
StringBuilder sb = new StringBuilder();

sb.AppendFormat("{0} {1}, "Jeffrey", "Richter").Replace(' ', '-');

String s = sb.ToString().ToUpper();

sb.Length = 0;

sb.Append(s).Insert(8, "Marc-");

s = sb.ToString();

Console.WriteLine(s);
~~~

### 객체에 대한 문자열 표현을 얻어오기: ToString
객체에 대한 문자열 표기를 얻어와야 할 경우가 생각보다 자주 있을 것이다. 보통은 숫자 값(Byte, Int32, Single과 같은)이나 DateTime 객체를 사용자에게 보여주어야 할 때 이러한 기능이 필요하다.

객체의 현재 상태를 문자열로 표현하려는 모든 타입에 대해서 가장 적당한 구현 방법은 ToString 메서드를 재정의하는 것이다. FCL 안에 내장되어 있는 상당수의 핵심 타입들(Byte, Int32, UInt64, Double 등)은 모두 ToString 메서드를 재정의하여 스레드의 문화권에 부합하는 문자열을 반환하도록 구현하고 있다.

#### 특정 서식과 문화권
매개변수가 없는 ToString 메서드에는 두 가지 문제점이 있다. 첫째, 문자열에 대한 서식을 제어할 수 있는 방법이 없다. 예를 들어 응용프로그램에서 숫자를 특정 통화 기호, 백분율 기호, 혹은 16진수 문자열로 변환하려고 할 때 문제가 될 수 있다. 둘째, 특정 문화권에 맞춘 서식을 선택할 방법이 마땅치 않다. 이 문제는 클라이언트 측 응용프로그램보다는 특히 서버 측 응용프로그램에서 더 곤란한 문제다.

System.IFormattable 인터페이스를 구현하고 있는 타입들은 서식이나 문화권을 설정할 수 있는 ToString을 구현하고 있다.
~~~
public interface IFormattable {
    String ToString(String format, IFormatProvider formatProvider);
}
~~~

#### 여러 객체를 문자열로 변환하기
문자열 생성 시에 한번에 여러 객체의 서식을 지정해야 할 때도 있을수 있다.
~~~
String s = String.Format("On {0}, {1} is {2} years old.", new DateTime(2012, 4, 22, 14, 35, 5), "Aidan", 9);
Console.WriteLine(s);
~~~
만약 이 코드를 현재 스레드의 문화권 정보가 "en-US"인 상태에서 실행한다면, 다음과 같은 문자열이 표시될 것이다.
~~~
On 4/22/2012 2:35:05 PM, Aidan is 9 years old.
~~~
문자열을 생성 하는 과정을 좀 더 세부적으로 설정 하려면 중괄호 안에 서식 정보를 지정할 수도 있다.
~~~
String s = String.Format("On {0:D}, {1} is {2:E} years old.", new DateTime(2012, 4, 22, 14, 35, 5), "Aidan", 9);
Console.WriteLine(s);
~~~
이 코드를 컴파일하여 현재 스레드의 문화권 정보가 "en-US"인 상태에서 다시 실행하면, 다음과 같은 문자열이 표시 될 것이다.
~~~
On Sunday, April 22, 2012, Aidan is 9.000000E+000 years old.
~~~

#### 사용자 정의 서식 지정 구현하기
StringBuilder의 AppendFormat 메서드를 사용하는 경우, 각각의 객체를 문자열로 변환해야할 때마다 호출되는 메서드를 정의할 수 있다. 즉 ToString 메서드를 호출하는 것이 아니라, 사용자가 정의한 메서드를 호출하도록 하여 자유롭게 문자열을 생성하도록 할 수 있다는 것이다. 이 내용은 String의 Format 메서드에도 동일하게 적용된다.
~~~
using System;
using System.Text;
using System.Threading;

public static class Program {
    public static void Main() {
        StringBuilder sb = new StringBuilder();
        sb.AppendFormat(new BoldInt32s(), "{0} {1} {2:M}", "Jeff", 123, DateTime.Now);
        Console.WriteLine(sb);
    }
}

internal sealed class BoldInt32s : IFormatProvider, ICustomFormatter {
    public Object GetFormat(Type formatType) {
        if (formatType == typeof(ICustomFormatter)) return this;
        return Thread.CurrentThread.CurrentCulture.GetFormat(formatType);
    }

    public String Format(String format, Object arg, IformatProvider formatProvider) {
        String s;
        
        IFormattable formattable = arg as IFormattable;
        if (formattable == null) s = arg.ToString();
        else s = formattable.ToString(format, formatProvider);

        if (arg.GetType() == typeof(Int32))
            return "<B>" + s + "</B>";
        return s;
    }
}
~~~

### 인코딩: 문자 배열과 바이트 배열 사이의 변환
FCL은 문자를 쉽게 인코딩하고 디코딩을 할 수 있는 몇가지 타입들을 제공하고 있다. 가장 많이 사용하는 인코딩은 UTF-16과 UTF-8이다.
* UTF-16은 16비트 문자를 2바이트로 인코딩한다. 문자에는 영향을 주지 않으며, 압축도 발생하지 않아 성능상으로는 가장 훌륭하다. 이 같은 UTF-16 인코딩을 유니코드 인코딩이라고도 한다. UTF-16은 또한 리틀 에디안(little endian)과 빅 에디안(big endian) 사이를 변환하기 위한 용도로도 사용된다.
* UTF-8은 글자들을 종류에 따라서 1바티으부터 4바이트로 인코딩을 수행한다. UTF-8은 매우 대중적인 인코딩이지만, 0x0800 또는 그 이상의 문자 값을 인코딩할 경우 UTF-16에 비해 비효율적이다.
* 
다음은 UTF-8을 사용하여 문자열을 인코딩하고 디코딩하는 예제다.
~~~
using System;
using System.Text;

public static class Program {
    public static void Main() {
        String s = "Hi there";

        Encoding encodingUTF8 = Encoding.UTF8;

        Byte[] encodedBytes = encodingUTF8.GetBytes(s);

        Console.WriteLine("Encoded bytes: " + BitConverter.ToString(encodedBytes));

        String decodedString = encodingUTF8.GetString(encodedBytes);

        Console.WriteLine("Decoded string: " + decodedString);
    }
}
~~~

#### Base-64 문자열 인코딩과 디코딩
UTF-16과 UTF-8 인코딩뿐 아니라 또한 바이트 배열을 Base-64 문자열로 인코딩하는 것도 상당히 광범위하게 사용되고 있다. FCL 또한 Base-64 인코딩과 디코딩을 수행하는 메서드를 제공하고 있다. 이 또한 Encoding을 상속하 ㄴ형태로 구현되었을 것이라 예상할 수 있겠지만, 몇가지 이유에서 Base-64 기반 인코딩과 디코딩은 System.Convert 타입이 제공하는 정적 메서드를 통해서 수행된다.
~~~
using System;

public static class Program {
    public static void Main() {
        // 임의로 10바이트를 생성한다.
        Byte[] bytes = new Byte[10];
        new Random().NextBytes(byte);

        Console.WriteLine(BitConverter.ToString(bytes));

        String s = Convert.ToBase64String(bytes);
        Console.WriteLine(s);

        bytes = Convert.FromBase64String(s);
        Console.WriteLine(BitConverter.ToString(bytes));
    }
}
~~~

#### 안전한 문자열
마이크로소프트에서는 조 ㅁ더 보안상 안전한 문자열 클래스를 FCL에 새로 추가하는데, 그것이 System.Security.SecureString 클래스다. SecureString 객체를 만들면, 내부적으로 비관리 메모리 영역에 문자의 배열을 할당한다. 이 비관리 메모리 영역은 가비지 컬렉터가 관리하지 않는다.

이 문자열들은 암호화되기 때문에, 불순한 목적을 가진 안전하지 않고/관리되지 않은 코드로부터 보안에 민감한 정보를 보호할 수 있다. 문자열 내부의 상태가 아주 짧은 시간 동안만 암호화되지 않은 상태로 유지된다. 그리고 이러한 특성으로 인해 각각의 작업 성능이 현저히 느리므로 가능한 적게 사용하는 것이 좋다.