---
title: clrviacharp-chapter15
tags:
---
### 열거 타입
**열거 타입**은 기호 이름과 연결되는 값의 쌍을 묶어서 정의하는 타입이다.
~~~
internal enum Color {
    White,      // 숫자 값 0이 할당된다.
    Red,
    Green,
    Blue,
    Orange
}
~~~
* 열거 타입을 사용하면 프로그램을 좀 더 쓰고 읽고 관리하게 쉽게 해준다.
* 열거 타입은 보통 강력한 타입으로 취급된다.

모든 열거 타입은 System.Enum 타입을 상속하는데, System.Enum은 System.ValueType을 상속하고, System.ValueType은 최종적으로 System.Object를 상속한다. 그래서 열거 타입들은 값 타입으로 분류되며, 박싱된 형태와 박싱되지 않은 형태 모두로 표현할 수 있다.

System.Enum의 정적 메서드인 GetValues나 System.Type의 인스턴스 메서드인 GetEnumValus를 이용하면 열거 타입 내에서 정의하고 있는 개별 기호들을, 배열 요소로 저장하고 있는 배열을 가져올 수 있다.
~~~
public static Array GetValue(Type enumType);    // System.Enum에 정의 되어 있다.
public Array GetEnumValues();                   // System.Type에 정의되어 있다.

Color[] colors = (Color[]) Enum.GetValues(typeof(Color));
Console.WriteLine("Number of symbols defined: " + colors.Length);
Console.WriteLine("Value\tSymbol\n-----\t-----");
foreach (Color c in colors) {
    Console.WriteLine("{0,5:D}\t{0:G}", c);
}
~~~

개인적으로 GetValues와 GetEnumValues 메서드를 좋아하지 않는데, 양쪽 메서드 모두 Array 타입으로 결과를 반환하기 때문에, 정확한 배열 요소 타입으로 반드시 캐스팅을 수행해야 하기 때문이다.
~~~
public static TEnum[] GetEnumValue<TEnum>() where TEnum : struct {
    return (TEnum[])Enum.GetValues(typeof(TEnum));
}
~~~