---
title: clrviacharp-chapter17
tags:
---
.NET Framework는 콜백 함수 메커니즘을 **델리게이트**라는 형태로 노출하고 있다. 네이티브 C++ 등의 다른 플랫폼이 제안하는 콜백 메커니즘과는 다르게 델리게이트는 더 강력한 기능을 제공한다. 예를 들어 델리게이트는 콜백 메서드의 타입 안정성을 보장함으로써, CLR의 가장 중요한 원칙 중 하나를 지키고 있다. 델리게이트는 또한 여러 메서드를 순차적으로 호출할 수 있는 기능을 내장하고 있으며 인스턴스 메서드뿐 아니라 정적 메서드도 호출할 수 있따.

### 델리게이트 살펴보기
~~~
using System;
using System.Windows.Forms;
using System.IO;

// 델리게이트 타입을 하나 정의한다. Int32 타입의 매개변수 하나를 받고, 따로 반환 값이 없음을 표시한다.
internal delegate void Feedback(Int32 value);

public sealed class Program {
    public static void Main() {
        StaticDelegateDemo();
        InstanceDelegateDemo();
        ChainDelegateDemo1(new Program());
        ChainDelegateDemo2(new Program());
    }

    private static void StaticDelegateDemo()
    {
        Console.WriteLine("----- Static Delegate Demo -----");
        Counter(1, 3, null);
        Counter(1, 3, new Feedback(Program.FeedbackToConsole));
        Counter(1, 3, new Feedback(FeedbackToMsgBox));   // "Program." 문구는 선택사항이다.
        Console.WriteLine();
    }

    private static void InstanceDelegateDemo()
    {
        Console.WriteLine("----- Instance Delegate Demo -----");
        Program p = new Program();
        Counter(1, 3, new Feedback(p.FeedbackToFile));   // "Program." 문구는 선택사항이다.
        Console.WriteLine();
    }

    private static void Counter(Int32 from, Int32 to, Feedback fb)
    {
        for (Int32 val = from; val <= to; val++)
        {
            // 콜백이 하나라도 있다면 호출된다.
            if (fb != null)
                fb(val);
        }
    }

    private static void ChainDelegateDemo1(Program p)
    {
        Console.WriteLine("----- Chanin Delegate Demo 1 -----");
        Feedback fb1 = new Feedback(FeedbackToConsole);
        Feedback fb2 = new Feedback(FeedbackToMsgBox);
        Feedback fb3 = new Feedback(p.FeedbackToFile);

        Feedback fbChain = null;
        fbChain = (Feedback)Delegate.Combine(fbChain, fb1);
        fbChain = (Feedback)Delegate.Combine(fbChain, fb2);
        fbChain = (Feedback)Delegate.Combine(fbChain, fb3);
        Counter(1, 2, fbChain);

        Console.WriteLine();
        fbChain = (Feedback)Delegate.Remove(fbChain, new Feedback(FeedbackToMsgBox));
        Counter(1, 2, fbChain);
    }

    private static void ChainDelegateDemo2(Program p)
    {
        Console.WriteLine("----- Chanin Delegate Demo 2 -----");
        Feedback fb1 = new Feedback(FeedbackToConsole);
        Feedback fb2 = new Feedback(FeedbackToMsgBox);
        Feedback fb3 = new Feedback(p.FeedbackToFile);

        Feedback fbChain = null;
        fbChain += fb1;
        fbChain += fb2;
        fbChain += fb3;
        Counter(1, 2, fbChain);

        Console.WriteLine();
        fbChain -= new Feedback(FeedbackToMsgBox);
        Counter(1, 2, fbChain);
    }

    private static void FeedbackToConsole(Int32 value)
    {
        Console.WriteLine($"Item={value}");
    }

    private static void FeedbackToMsgBox(Int32 value)
    {
        MessageBox.Show($"Item={value}");
    }

    private void FeedbackToFile(Int32 value)
    {
        using (StreamWriter sw = new StreamWriter("Status", true))
        {
            sw.WriteLine($"Item={value}");
        }
    }
}
~~~

### 정적 메서드에 대한 콜백을 델리게이트로 구현하기
C#과 CLR은 메서드를 델리게이트에 바인딩할 때 참조 타입에 대한 공변성과 반공변성을 허용한다. 공변성이란 델리게이트의 원형에서 정의한 반환 타입을 상속하는 타입을 반환하는 메서드를 델리게이트에 바인드할 수 있음을 나타내는 성질이다. 반공변성이란 델리게이트의 원형에 정의한 매개변수의 부모타입을 매개변수로 받는 메서드를 델리게이트에 바인드할 수 있음을 나타내는 성질이다.

참고할 것은 공변성과 반공변성은 참조 타입에 한해서만 허용되며, 값 타입이나 반환 타입이 없음을 표현하는 void 타입에 대해서는 적용되지 않는다는 것이다.

### 델리게이트 파헤쳐보기
> **중요**: System.MulticastDelegate 클래스는 System.Delegate를 상속하며, 궁극적으로는 System.Object로부터 상속된다. FCL에서의 델리게이트는ㄴ 하나의 개념이지만 왜 델리게이트에 관련된 클래스가 두 개나 존재하는지에 대해 설명하자면, 나름의 사정이 있다. 델리게이트가 MulticastDelegate 타입을 부모로 택하여 상속을 받고 있음에도 불구하고, 안타깝게도 양쪽의 클래스에 대해 항상 고려해야 할 필요가 있는데, 가끔 델리게이트 타입을 메서드에서 사용하기 위하여 MulticastDelegate 클래스 대신 Delegate 클래스를 사용해야 할 수도 있다. 예를 들어 Delegate 클래스는 Combine과 Remove라는 정적 메서드를 제공한다. 두 메서드의 원형을 살펴보면 Delegate 타입의 매개변수를 받도록 정의되어 있다. 사용자가 정의하는 델리게이트 타입은 MulticastDelegate 타입으로부터 상속받았고, 다시 Delegate 타입으로부터 상속받은 것이기 때문에, 사용자가 정의한 델리게이트 타입을 미세더드들로 전달할 수 있다.

모든 델리게이트 타입이 MulticastDelegate 타입을 상속하기 때문에, MulticastDelegate의 필드, 속성, 메서드도 같이 상속을 받는다.

| 필드 | 타입 | 설명 |
| :- | :- | :- |
| _target | System.Object | 델리게이트 객체가 정적 메서드를 포장하는 경우, 이 필드는 null을 가지게 된다. 만약 델리게이트 객체가 인스턴스 메서드를 포장하는 경우, 이 필드는 콜백 메서드가 호출되어야 할 대상 객체에 대한 참조를 가리키게 된다. 바꾸어 말하면, 이 필드는 인스턴스 메서드에 암묵적으로 항상 전달되어야 하는 this 매개변수와 같다. |
| _methodPtr | System.IntPtr | CLR이 콜백으로 호출해야 하는 메서드를 식별하는 내부 정수 필드다. |
| _invocationList | System.Object | 이 필드는 보통 null로 설정된다. 델리게이트 체인을 만들기 위한 배열을 가리킬 수 있다. |

### 델리게이트를 사용하여 여러 메서드를 호출하기(메서드 연결하기)
#### 델리게이트 체인 호출을 커스터마이징하는 방법
델리게이트 타입의 Invoke 메서드에서 내부적으로 _invocationList에 할당된 델리게이트 배열 전체를 순회하면서 개별 델리게이트 항목에 대하여 Invoke를 수행하기 때문에, 체인 내의 포함되어 있는 모든 항목들이 호출되게 된다. 이러한 방식은 상당히 간결한 방식이고 대부분의 시나리오에서 적합할지 모르겠으나, 상당히 많은 제약이 있는 것도 사실이다. 대표적인 제약으로는 콜백 메서드의 반환 값이 가장 마지막 것을 제외하고는 모두 소실되는 것을 들 수 있겠다.게다가 이것이 유일한 제약인 것도 아니다. 만약 호출하는 메서드를 실행하는 중에 예외가 발생하거나 메서드 실행이 너무 오래 걸린다면 무슨 일이 일어날까? 알고리즘 체인 내의 모든 항목을 호출해야 함에도 불구하고, 그러지 못하고 중간에 방해를 받을 가능성이 있다는 것이다. 당연한 얘기지만 이런 알고리즘은 견고하지 못하다.

기본 알고리즘이 적절하지 않은 상황에 대비하기 위해서 MulticastDelegate  클래스는 인스턴스 메서드로 GetInvocationList라는 메서드를 제공하는데, 이 메서드를 이용하면 체인의 각 델리게이트 항목을 명시적으로 호출할 수 있을 뿐만 아니라, 순회 방식을 다른 알고리즘으로 변경 할 수도 있다.
~~~
public abstract class MulticastDelegate : Delegate {
    // 델리게이트 체인 내의 항목들을 델리게이트 객체 배열로 만들어 반환한다.
    public sealed override Delegate[] GetInvocationList();
}
~~~

이제 이 배열을 이용하면 개별 델리게이트를 호출하기 위한 알고리즘을 쉽게 작성할 수 있다.
~~~
using System;
using System.Reflection;
using System.Text;

internal sealed class Light
{
    public String SwitchPosition()
    {
        return "The light is off";
    }
}

internal sealed class Fan
{
    public String Speed()
    {
        throw new InvalidOperationException("The fan broke due to overheadting");
    }
}

internal sealed class Speaker
{
    public String Volume()
    {
        return "The volume is loud";
    }
}

internal sealed class Program
{
    private delegate String GetStatus();

    private static void Main()
    {
        GetStatus getStatus = null;

        getStatus += new GetStatus(new Light().SwitchPosition);
        getStatus += new GetStatus(new Fan().Speed);
        getStatus += new GetStatus(new Speaker().Volume);

        Console.WriteLine(GetComponentStatusReport(getStatus));
    }

    private static String GetComponentStatusReport(GetStatus status)
    {
        if (status == null) return null;

        StringBuilder report = new StringBuilder();

        Delegate[] arrayOfDelegate = status.GetInvocationList();

        foreach (GetStatus getStatus in arrayOfDelegate)
        {
            try
            {
                report.AppendFormat($"{getStatus()}{Environment.NewLine}{Environment.NewLine}");
            }
            catch (InvalidOperationException e)
            {
                Object component = getStatus.Target;
                report.AppendFormat("Failed to get status from {1}{2}{0} Errors: {3}{0}{0}", 
                    Environment.NewLine, ((component == null) ? "" : component.GetType() + "."), getStatus.GetMethodInfo().Name, e.Message);
            }
        }

        return report.ToString();
    }
}
~~~

### 이미 정의되어 있는 델리게이트 활용하기(제네릭 델리게이트)
.NET Framework가 제네릭을 지원하기 때문에 몇 종류의 일반화된 델리게이트들을 이용해서 최대 열여섯 개의 매개변수를 받는 메서드들을 모두 가리킬 수 있다.
~~~
public delegate void Action();      // 제네릭이 아닌 경우도 있다.
public delegate void Action<T>(T obj);
...
public delegate void Action<T1, ..., T16>(T1 arg, ..., T16 arg16);
~~~

Action 델리게이트뿐 아니라 .NET Framework는 열입곱 가지 Func 델리게이트들도 제공하는데, 이 경우는 콜백 메서드가 반환 값을 가지는 경우을 대비하기 위한 것이다.
~~~
public delegate TResult Func<TResult>();
public delegate TResult Func<T, TResult>(T arg);
...
public delegate TResult Func<T1, ..., T16, TResult>(T1 arg1, ..., T16 arg16);
~~~
이제 개발자들이 새로이 델리게이트 타입들을 정의하기 보다는 이미 정의되어 있는 이 같은 델리게이트 타입을 사용할 것을 권고하고 있다. 이렇게 하면 시스템에 포함하는 타입의 숫자를 줄일 수 있고, 코딩을 더욱 간결하게 할 수 있다. 하지만 매개변수를 ref나 out 키워드로 정의하는 메서드를 가리키기 위해서는 여전히 고유의 델리게이트를 따로 정의해야만 한다.
~~~
delegate void Bar(ref Int32 z);
~~~
또한 C#의 params 키워드를 사용하여 가변 인자를 델리게이트를 통해서 받기를 원할 때에도 직접 델리게이트를 정의해야 하며, 델리게이트의 매개변수에 기본값을 지정하기를 원하거나, 델리게이트 제네릭 타입 인자에 제약조건을 추가하려는 경우에도 직접 델리게이트를 정의해야만 한다.

## 델리게이트를 위한 C#의 문법적 편의사항
### 문법적 편의사항 #1: 델리게이트 객체를 생성할 필요가 없다.
C#은 델리게이트 객체 생성을 하지 않고 콜백 메서드의 이름을 직접 지정하는 것을 허용한다.
~~~
internal sealed class AClass {
    public static void CallbackWithoutNewingADelegateObject() {
        ThreadPool.QueueUserWorkItem(SomeAsyncTask, 5);
    }

    private static void SomeAsyncTask(Object o) {
        Console.WriteLine(o);
    }
}
~~~
여기에서 ThreadPool 클래스의 정적 메서드인 QueueUserWorkItem 메서드는 WaitCallback 델리게이트 객체를 매개변수로 요구하기 때문에 SomeAsyncTask 메서드를 포장한 객체의 참조를 전달해야 한다. 하지만 C# 컴파일러는 이러한 상황에서 무엇을 해야 하는지 유추할 수 있으므로, WaitCallback 델리게이트 객체를 생성하는 작업을 생략하더라도 이를 대신 처리해준다. C# 컴파일러는 자동으로 WaitCallback 델리게이트 객체를 생성하고 이 객체의 참조를 전달하도록 IL코드를 만들어주며, 단순히 문법적 편의 장치(Syntactic Sugar)를 활용한 것에 불가하다.

### 문법적 편의사항 #2: 콜백 메서드를 정의하지 않아도 된다(람다표현식)
C#은 콜백 메서드를 인라인 방식으로 작성할 수 있도록 해주기 때문에, 고유의 메서드를 따로 정의하지 않아도 된다.
~~~
internal sealed class AClass {
    public static void CallbackWithoutNewingADelegateObject() {
        ThreadPool.QueueUserWorkItem(obj => Console.WriteLine(obj), 5);
    }
}
~~~
C#의 람다 표현식 연산자인 => 기호를 사용하고 있기 때문에 이것이 람다 표현식이라는 것을 쉽게 인지 할 수 있다. 코드에서 람다 표현식을 사용하면 컴파일러는 자동으로 이를 델리게이트로 인지한다.컴파일러는 람다 표현식을 확인하면 자동으로 해당 클래스에 새로운 private 메서드를 하나 추가한다. 이 메서드를 익명 메서드(Anonymous Function)라고 부른다.

람다표현식은 반드시 WaitCallback 델리게이트와 일치해야 한다. 즉 하나의 object 매개변수만 받아야 하고 반환 값이 없어야 한다. 또한 익명 메서드가 private으로 기록된다는 것을 다시 한번 주목하기 바란다. 이를 통해 이 메서들르 해당 클래스 밖의 다른 코드에서 접근할 수 없도록 보호한다. 또한 익명 메서드가 정적 메서드로 표시되어 있다는 점도 같이 주목할 부분인데 코드가 인스턴스 멤버들을 전혀 접근하지 않기 때문이다. 하지만 이 코드는 내부의 임의의 정적 필드나 메서드를 얼마든지 접근 할 수 있다.
~~~
internal sealed class AClass {
    public static String m_name;

    public static void CallbackWithoutNewingADelegateObject() {
        ThreadPool.QueueUserWorkItem(
            obj => Console.WriteLine(m_name + ": " + obj),
            5);
    }
}
~~~

만약 CallbackWithoutNewingADelegateObject 메서드가 정적 메서드가 아니라면, 익명 메서드의 코드는 인스턴스 멤버를 참조할 수 있다. 인스턴스 멤버를 참조하지 않는다면, 컴파일러는 여전히 정적 메서드로 익명 메서드로 정의하게 되는데 왜냐하면 이렇게 하는 것이 인스턴스 메서드를 호출하기 위하여 암묵적으로 전달해야만 하는 암묵적 매개변수 this를 생략할 수 있어 더 효율적이기 때문이다.

~~~
internal sealed class AClass {
    public String m_name;

    public void CallbackWithoutNewingADelegateObject() {
        ThreadPool.QueueUserWorkItem(
            obj => Console.WriteLine(m_name + ": " + obj),
            5);
    }
}
~~~

람다 표현식 => 연산자의 왼 편에 오는 이름들은 람다 표현식 안으로 전달될 매개변수들의 목록을 나타낸다. 여기에는 반드시 지켜야 할 몇 가지 규칙들이 있다.

~~~
// 만약 델리게이트가 매개변수를 받지 않는다면 ()로 표기한다.
Func<String> f = () => "Jeff";

// 만약 한 개 이상의 매개변수를 받는다면 명시적으로 매개변수의 타입을 지정할 수 있다.
Func<Int32, String> f2 = (Int32 n) => n.ToString();
Func<Int32, Int32, String> f3 = (Int32 n1, Int32 n2) => (n1 + n2).ToString();

// 만약 한 개 이상의 매개변수를 받는다면 매개변수 타입 이름을 컴파일러가 자동으로 유추하도록 할 수도 있다.
Func<Int32, String> f4 = (n) => n.ToString();
Func<Int32, Int32, String> f5 = (n1, n2) => (n1 + n2).ToString();

// 만약 델리게이트가 한 개의 매개변수만을 받는다면, () 기호를 생략할 수 있다.
Func<Int32, String> f6 = n => n.ToString();

// 만약 델리게이트가 ref나 out 형태로 매개변수를 사용한다면,
// ref/out과 함께 매개변수의 타입과 이름을 모두 사용해야 한다.
delegate void Bar(out Int32 z);
Bar b = (out Int32 n) => n = 5;
~~~

만약 메서드 본문을 하나 이상의 문장으로 구성하기를 원한다면, 반드시 중괄호를 열고 닫아 구분해 주어야 한다. 그리고 델리게이트가 반환 값을 요구한다면, 반드시 return문을 메서드 본문안에 기재해야 한다.
~~~
Func<Int32, Int32, String> f7 = (n1, n2) => { Int32 sum = n1 + n2; return sum.ToString() };
~~~

### 문법적 편의사항 #3: 클래스 내의 로컬 변수를 포장하여 명시적으로 콜백 메서드로 전달할 필요가 없다.\
콜백 함수의 코드가 정의된 메서드 내의 로컬 매개변수나 그 변수를 참조해야 하는 경우가 있다.
~~~
internal sealed class AClass {
    public static void UsingLocalVariablesIntheCallbackCode(Int32 numToDo)
    {
        Int32[] squares = new Int32[numToDo];
        AutoResetEvent done = new AutoResetEvent(false);

        for(Int32 n = 0; n < squares.Length; n++)
        {
            ThreadPool.QueueUserWorkItem(
                obj =>
                {
                    Int32 num = (Int32)obj;

                    // 이 작업은 보통 시간이 더 걸리기 마련이다.
                    squares[num] = num * num;

                    // 만약 마지막 작업인 경우, 메인 스레드가 계속 실행되도록 한다.
                    if (Interlocked.Decrement(ref numToDo) == 0)
                    {
                        done.Set();
                    }

                }, n);
        }

        // 나머지 모든 스레드들의 작업 끝날 때까지 기다린다.
        done.WaitOne();

        for (Int32 n = 0; n < squares.Length; n++)
        {
            Console.WriteLine($"Index {n}, Square={squares[n]}");
        }
    }
}
~~~

> **중요**: 의심할 여지 없이, 프로그래머가 C#의 람다 표현식을 남용하거나 오용해서는 안된다. 메서드 내에 람다 표현식을 사용하면 마치 그것이 메서드 내에 작성된 것처럼 보이지만 실제로는 그렇지 않아서 디버깅이 어려울 수 있으며, 코드 내부를 한 단계씩 진행하는 것이 상당히 어려울 수 있다.

> 만약 콜백 메서드에 포함할 메서드의 코드가 세 줄 이상이라면, 람다 표현식을 사용하지 않고 직접 추가 메서드를 정의하기로 하였다. 람다 표현식이 없었다면 다음의 코드는 작성하기에 거추장스럽고 읽기도 어려우며 관리하기에도 불편했을 것이다.

~~~
String[] names = {"Jeff", "Kristin", "Aidan", "Grant" };

// 항목들 중 소문자 'a'가 들어있는 이름들을 가져온다.
Char charToFind = 'a';
names = Array.FindAll(names, name => name.IndexOf(charToFind) >= 0);

// 각각의 문자열들을 모두 대문자로 바꾼다.
names = Array.ConvertAll(names, name => name.ToUpper());

// 실행 결과를 표시한다.
Array.ForEach(names, Console.WriteLine);
~~~

## 델리게이트와 리플렉션
지금까지 다룬 내용은 델리게이트를 사용하기 위해서는 메서드의 프로토타입을 알아야 하며, 이를 통해서 호출이 가능하다는 것이었다. 개발자는 반드시 콜백 메서드에 얼마나 매개변수들을 전달해야 하는지, 그리고 각 매개변수들의 타입이 무엇인지를 정확히 알고 있어야 한다. 다행스럽게도, 대개 개발자들은 이러한 정보들을 모두 알고 있으므로, 코드를 작성하는 것은 문제될 것이 없다.

그러나 일부 특별한 상황에서는 개발자들이 이러한 정보들을 컴파일 시점에 확인할 방법이 마땅치 않은 경우가 있다.

다행스럽게도, System.Reflection.MethodInfo는 CreateDelegate라는 메서드를 제공하여 컴파일 시점에는 델리게이트에 대해서 충분히 알 수 없는 상황일지라도 실행할 때 델리게이트를 만들 수 있는 방법을 제공한다.
~~~
public abstract class MethodInfo : MethodBase {
    // 정적 메서드를 포장하는 델리게이트 객체를 만든다.
    public virtual Delegate CreateDelegate(Type delegateType);

    // 인스턴스 메서드를 포장하는 델리게이트 객체를 만들며,
    // target에는 this로 전달할 값을 매개변수로 지정한다.
    public virtual Delegate CreateDelegate(Type delegateType, Object target)
}
~~~
델리게이트를 만든 이후에는 Delegate의 DynamicInvoke 메서드를 사용하여 호출할 수 있다.
~~~
public abstract class Delegate {
    public Object DynamicInvoke(params Object[] args);
}
~~~
리플렉션 API를 사용하려면, 먼저 델리게이트로 포장하려는 메서드의 MethodInfo 객체를 얻어와야 한다. 다음으로 CreateDelegate 메서드를 호출해야 하는데, 이때 첫 번째 매개변수인 delegateType으로 Delegate를 상속한 타입을 지정해야 한다. 델리게이트가 인스턴스 메서드를 포장하는 경우에는 CreateDelegate의 target 매개변수로 인스턴스 메서드에 this로 전달할 객체를 매개변수로 전달해야 한다. 그러면 CreateDelegate 메서드는 첫번째 매개변수인 delegateType으로 확인한 타입의 새로운 객체를 생성한다.

System.Delegate의 DynamicInvoke 메서드를 이용하면 델리게이트 객체가 포장하고 있는 콜백 메서드를 호출할 수 있는데, 컴파일 시점에는 알 수 없지만 실행 시점에 파악할 수 있는 매개변수들을 모아 배열로 전달할 수 있다.

다음의 코드는 CreateDelegate와 DynamicInvoke 메서드의 사용법을 보여준다.
~~~
internal delegate Object TwoInt32s(Int32 n1, Int32 n2);
internal delegate Object OneString(String s1);

internal class Program
{
    private static void Main(string[] args)
    {
        if (args.Length < 2)
        {
            String usage =
                @"Usage:" +
                "{0} delType methodName [Arg1] [Arg2]" +
                "{0} where delType must be TwoInt32s or OneString" +
                "{0} if delType is TwoInt32s, methodName must be Add or Subtract" +
                "{0} if delType is OneString, methodName must be NumChars or Reverse" +
                "{0}Examples:" +
                "{0} TwoInt32s Add 123 221" +
                "{0} TwoInt32s Substract 123 321" +
                "{0} OneString NumChar \"Hello there\"" +
                "{0} OneString Reverse \"Hello there\"";
            Console.WriteLine(usage, Environment.NewLine);
            return;
        }

        // delType 매개변수를 델리게이트 타입으로 변환한다.
        Type delType = Type.GetType(args[0]);
        if (delType == null)
        {
            Console.WriteLine("Invalid delType argument: " + args[0]);
            return;
        }

        Delegate d;
        try
        {
            // Arg1 매개변수를 메서드로 변환한다.
            MethodInfo mi = typeof(Program).GetTypeInfo().GetDeclaredMethod(args[1]);

            // 델리게이트 객체를 만들고 정적 메서드를 포장한다.
            d = mi.CreateDelegate(delType);
        }
        catch (ArgumentException)
        {
            Console.WriteLine("Invalid methodName argument: " + args[1]);
            return;
        }

        // 델리게이트 객체를 통하여 메서드로 전달하려는 매개변수들을 모아 배열로 만들고 전달한다.
        Object[] callbackArgs = new Object[args.Length - 2];

        if (d.GetType() == typeof(TwoInt32s))
        {
            try
            {
                for (Int32 a = 2; a < args.Length; a++)
                    callbackArgs[a - 2] = Int32.Parse(args[a]);
            }
            catch (FormatException)
            {
                Console.WriteLine("Parameters must be integers.");
                return;
            }
        }

        if (d.GetType() == typeof(OneString))
        {
            Array.Copy(args, 2, callbackArgs, 0, callbackArgs.Length);
        }

        try
        {
            object result = d.DynamicInvoke(callbackArgs);
            Console.WriteLine("Result = " + result);
        }
        catch (TargetParameterCountException)
        {
            Console.WriteLine("Incorrect number of parameters specified.");
        }
    }

    private static Object Add(Int32 n1, Int32 n2)
    {
        return n1 + n2;
    }

    private static Object Subtract(Int32 n1, Int32 n2)
    {
        return n1 - n2;
    }

    private static Object NumChars(String s1)
    {
        return s1.Length;
    }

    private static Object Reverse(String s1)
    {
        return new String(s1.Reverse().ToArray());
    }
}
~~~