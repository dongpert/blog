---
title: netguid-anonymous-methods
tags:
---
In versions of C# before 2.0, the only way to declare a delegate was to use named methods. C# 2.0 introduced anonymous methods and in C# 3.0 and later, lambda expressions supersede anonymous methods as the preferred way to write inline code. However, the information about anonymous methods in this topic also applies to lambda expressions. There is one case in which an anonymous method provides functionality not found in lambda expressions. Anonymous methods enable you to omit the parameter list. This means that an anonymous method can be converted to delegates with a variety of signatures. This is not possible with lambda expressions. For more information specifically about lambda expressions, see Lambda Expressions.

C# 2.0 버전 이전에 대리자를 선언하기 위한 유일한 바업은 명명된 메서드를 사용하는 것 이었습니다. C# 2.0은 익명 메서드를 소개했고 C# 3.0 이상에서는, 람다 식이 인라인 코드를 작성하는 선호하는 방식으로 익명 메서드를 대체합니다. 그러나 이 주제에서 익명 메서드에 관한 정보는 람다 식에도 적용됩니다. 익명 메서드가 람다 식에서 찾을 수 없는 기능을 제공하는 경우가 하나 있습니다. 익명 메서드는 매개변수 항목을 생략하는것을 허용합니다. 익명 메서드는 다양한 시그니처의 대리자로 변환 될수 있다는 것을 의미합니다. 이것은 람다 식으로는 불가능 합니다.

Creating anonymous methods is essentially a way to pass a code block as a delegate parameter. Here are two examples:

익명 메서드를 생성은 대리자 매개변수로 코드 블록을 전달하는 기본적인 방법입니다.
~~~
// Create a handler for a click event.
button1.Click += delegate(System.Object o, System.EventArgs e)
{ 
    System.Windows.Forms.MessageBox.Show("Click!");
};
~~~
~~~
// Create a delegate.
delegate void Del(int x);

// Instantiate the delegate using an anonymous method.
Del d = delegate(int k) { /* ... */ };
~~~
By using anonymous methods, you reduce the coding overhead in instantiating delegates because you do not have to create a separate method.

익명 메서드를 사용하면 별도의 메서드를 생성하지 않아도 되기 땜누에 대리자 초기화에서 코딩 오버헤드를 감소시킵니다.

For example, specifying a code block instead of a delegate can be useful in a situation when having to create a method might seem an unnecessary overhead. A good example would be when you start a new thread. This class creates a thread and also contains the code that the thread executes without creating an additional method for the delegate.

예를 들어 대리자 대신 코드 블럭을 지정하는 것은 불필요한 오버헤드처럼 보이는 메서드를 생성해야 하는 상황에서 유용할 수 있습니다. 좋은 예제는 새 쓰레드를 시작할 때 인 것 같다. 이 클래스는 쓰레드를 생성하고 대리자에 대한 추가적인 메서드 생성하지 않고 쓰레드를 실행하는 코드를 포함합니다.
~~~
void StartThread()
{
    System.Threading.Thread t1 = new System.Threading.Thread
      (delegate()
            {
                System.Console.Write("Hello, ");
                System.Console.WriteLine("World!");
            });
    t1.Start();
}
~~~
## Remarks
The scope of the parameters of an anonymous method is the anonymous-method-block.

무명 메서드의 매개변수의 범위는 무명 메서드의 블록입니다.

It is an error to have a jump statement, such as goto, break, or continue, inside the anonymous method block if the target is outside the block. It is also an error to have a jump statement, such as goto, break, or continue, outside the anonymous method block if the target is inside the block.

대상이 블록 외부에 있는 경우 무명 메서드 블록안에 goto, break, continue와 같은 점프 문을 가지는 것은 오류입니다. 대상이 블록 내부에 있는 경우 무명 메서드 블록 외부에 goto, break, continue와 같은 점프 문을 가지는 것은 오류입니다.

The local variables and parameters whose scope contains an anonymous method declaration are called outer variables of the anonymous method. For example, in the following code segment, n is an outer variable:

무명 메서드 선언을 포함하는 범위에 지역 변수와 매개변수 무명 메서드의 외부 변수라고 합니다.
~~~
int n = 0;
Del d = delegate() { System.Console.WriteLine("Copy #:{0}", ++n); };
~~~
A reference to the outer variable n is said to be captured when the delegate is created. Unlike local variables, the lifetime of a captured variable extends until the delegates that reference the anonymous methods are eligible for garbage collection.

외부 변수 n의 참조는 대리자가 생성되었을때 캡쳐된다고 합니다. 지역 변수와는 달리 캡처된 매개변수의 수명은 무명 메서드를 참조하는 대리자가 가비지 수집기에 대상이 될때까지 확장합니다.

An anonymous method cannot access the in, ref or out parameters of an outer scope.

무명 메서드는 외부 범위의 ref 또는 out 매개변수에 접근할 수 없습니다.

No unsafe code can be accessed within the anonymous-method-block.

Anonymous methods are not allowed on the left side of the is operator.

무명 메서드는 is 연산자의 왼쪽에 허용되지 않습니다.

## Example
The following example demonstrates two ways of instantiating a delegate:

다음 예제는 델리게이트를 인스턴스화 하는 두가지 방법을 보여줍니다.

* Associating the delegate with an anonymous method.
* Associating the delegate with a named method (DoWork).

In each case, a message is displayed when the delegate is invoked.
~~~
// Declare a delegate.
delegate void Printer(string s);

class TestClass
{
    static void Main()
    {
        // Instantiate the delegate type using an anonymous method.
        Printer p = delegate(string j)
        {
            System.Console.WriteLine(j);
        };

        // Results from the anonymous delegate call.
        p("The delegate using the anonymous method is called.");

        // The delegate instantiation using a named method "DoWork".
        p = new Printer(TestClass.DoWork);

        // Results from the old style delegate call.
        p("The delegate using the named method is called.");
    }

    // The method associated with the named delegate.
    static void DoWork(string k)
    {
        System.Console.WriteLine(k);
    }
}
/* Output:
    The delegate using the anonymous method is called.
    The delegate using the named method is called.
*/
~~~