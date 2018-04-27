---
title: nameof
tags:
---
Used to obtain the simple (unqualified) string name of a variable, type, or member.
변수, 형식 또는 멤버의 단순한 문자열 이름을 얻기 위해 사용됩니다.

When reporting errors in code, hooking up model-view-controller (MVC) links, firing property changed events, etc., you often want to capture the string name of a method. Using nameof helps keep your code valid when renaming definitions. Before, you had to use string literals to refer to definitions, which is brittle when renaming code elements because tools do not know to check these string literals.

에러를 보고, MVC 링크를 연결, 속성 변경 이벤트 실행 등을 할때 종종 메서드의 문자열 이름을 캡처하려고 합니다. nameof를 사용하면 당신의 코드를 유효하게 도와줍니다. 전에는 정의를 참조하기 하기 위해 당신은 문자열 리터럴을 사용해야만 했으며, 도구는 이러한 문자열 리터럴을 검사하는 방법을 알지 못하기 때문에 코드 요소의 이름을 변경할때 불안정합니다.

A nameof expression has this form: 
~~~
if (x == null) throw new ArgumentNullException(nameof(x));  
WriteLine(nameof(person.Address.ZipCode)); // prints "ZipCode"  
~~~

## Key Use Cases
These examples show the key use cases for nameof.

이 예제들은 nameof에 대한 중요한 사용 사례들을 보여줍니다.

Validate parameters: 
~~~
void f(string s) {  
   if (s == null) throw new ArgumentNullException(nameof(s));  
}  
~~~

MVC Action links: 
~~~
<%= Html.ActionLink("Sign up",  
            @typeof(UserController),  
            @nameof(UserController.SignUp))  
%>  
~~~

INotifyPropertyChanged: 
~~~
int p {  
   get { return this.p; }  
   set { this.p = value; PropertyChanged(this, new PropertyChangedEventArgs(nameof(this.p)); } // nameof(p) works too  
}  
~~~

XAML dependency property: 
~~~
public static DependencyProperty AgeProperty = DependencyProperty.Register(nameof(Age), typeof(int), typeof(C));
~~~

Logging: 
~~~
void f(int i) {  
   Log(nameof(f), "method entry");  
}  
~~~

Attributes: 
~~~
[DebuggerDisplay("={" + nameof(GetString) + "()}")]  
class C {  
   string GetString() { }  
} 
~~~

## Examples
Some C# examples: 
~~~
using Stuff = Some.Cool.Functionality  
class C {  
    static int Method1 (string x, int y) {}  
    static int Method1 (string x, string y) {}  
    int Method2 (int z) {}  
    string f<T>() => nameof(T);  
}  

var c = new C()  

nameof(C) -> "C"  
nameof(C.Method1) -> "Method1"   
nameof(C.Method2) -> "Method2"  
nameof(c.Method1) -> "Method1"   
nameof(c.Method2) -> "Method2"  
nameof(z) -> "z" // inside of Method2 ok, inside Method1 is a compiler error  
nameof(Stuff) = "Stuff"  
nameof(T) -> "T" // works inside of method but not in attributes on the method  
nameof(f) -> "f"  
nameof(f<T>) -> syntax error  
nameof(f<>) -> syntax error  
nameof(Method2()) -> error "This expression does not have a name"  
~~~

## Remarks
The argument to nameof must be a simple name, qualified name, member access, base access with a specified member, or this access with a specified member. The argument expression identifies a code definition, but it is never evaluated.

nameof에 인수는 간단한 이름, 적합한 이름, 멤버 접근, 지정된 멤버와의 기본 접근 또는 지정된 멤버와의 이 엑세스이여야 합니다. 인수표현식은 코드 정의를 식별하지만 절대 평가되지 않습니다.

Because the argument needs to be an expression syntactically, there are many things disallowed that are not useful to list. The following are worth mentioning that produce errors: predefined types (for example, int or void), nullable types (Point?), array types (Customer[,]), pointer types (Buffer*), qualified alias (A::B), and unbound generic types (Dictionary<,>), preprocessing symbols (DEBUG), and labels (loop:).

인수는 구문적으로 표현식일 필요가 있기때문에 허용되지 않는 것들이 많이 있습니다. 다음은 생산 오류를 언급할만한 가치가 있습니다. 미리지정된 형식(int or void), 널형식, 배열형식, 포인터 형식, 정규화된 별칭 그리고 바운드되지 않은 제네릭 형식, 전처리 기호, 레이블

If you need to get the fully-qualified name, you can use the typeof expression along with nameof. For example:
전체 정규화 이름을 얻을 필요가 있다면 typeof 표현식을 nameof와 함께 사용할 수 있습니다.
~~~
class C {
    void f(int i) {  
        Log($"{typeof(C)}.{nameof(f)}", "method entry");  
    }
}
~~~

Unfortunately typeof is not a constant expression like nameof, so typeof cannot be used in conjunction with nameof in all the same places as nameof. For example, the following would cause a CS0182 compile error:
불행히도 typeof는 nameof와 같이 상수 표현식이 아닙니다. 그래서 typeof는 nameof처럼 모든 같은 장소에서 nameof와 결합하여 사용될 수 없습니다.
~~~
[DebuggerDisplay("={" + typeof(C) + nameof(GetString) + "()}")]  
class C {  
   string GetString() { }  
}  
~~~

In the examples you see that you can use a type name and access an instance method name. You do not need to have an instance of the type, as required in evaluated expressions. Using the type name can be very convenient in some situations, and since you are just referring to the name and not using instance data, you do not need to contrive an instance variable or expression.
예제에서 형식 이름을 사용하고 인스턴스 메서드 이름에 접근할 수 있음을 알 수 있습니다. 평가된 표현식에서 필요한 것처럼 형식의 인스턴스를 가질 필요가 없습니다. 형식의 이름을 사용하면 몇몇 상황에서 매우 편리할 수 있고 이름을 참조하고 있고 인스턴스 데이터를 사용하지 않기 때문에 인스턴스 변수나 표현식을 설계할 필요가 없습니다.

You can reference the members of a class in attribute expressions on the class.

클래스의 멤버들을 클래스에 속성 표현식에서 참조할 수 있습니다.

There is no way to get a signatures information such as "Method1 (str, str)". One way to do that is to use an Expression, Expression e = () => A.B.Method1("s1", "s2"), and pull the MemberInfo from the resulting expression tree

"Method1(str, str)"와 같은 서명정보를 얻을 방법은 없습니다. 이것을 하기위한 하나의 방법은 표현식 Expression e = () => A.B.Method1("s1", s2")을 사용하는 것이고 결과 표현 트리에서 MemberInfo를 가져오는 것 입니다.

## Language Specifications 
For more information, see the C# Language Specification. The language specification is the definitive source for C# syntax and usage.
