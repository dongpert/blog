---
title: netguide-lambda-expressions
tags:
---
A lambda expression is an anonymous function that you can use to create delegates or expression tree types. By using lambda expressions, you can write local functions that can be passed as arguments or returned as the value of function calls. Lambda expressions are particularly helpful for writing LINQ query expressions.

람다 식은 대리자 또는 식 트리 형식을 생성하기 위해 사용할 수 있는 익명 함수입니다. 람다식을 사용하면 인수처럼 전달되거나 함수 호출의 값처럼 반환될 수 있는 지역 함수를 작성할 수 있습니다. 람다 식은 특히 LINQ 쿼리 식을 작성하는데 도움이 됩니다.

To create a lambda expression, you specify input parameters (if any) on the left side of the lambda operator =>, and you put the expression or statement block on the other side. For example, the lambda expression x => x * x specifies a parameter that’s named x and returns the value of x squared. You can assign this expression to a delegate type, as the following example shows:

람다 식을 작성하기 위해서 람다 연산자 => 왼쪽에 입력 매개변수를 지정해야 합니다. 그리고 다른쪽에 식이나 구문 블럭을 입력해야 합니다. 예를 들어 람다 식 x => x * x는 x라는 매개변수를 지정하고 x 제곱의 값을 반환합니다. 대리자 형식에 이 식을 할당할 수 있습니다.
~~~
delegate int del(int i);
static void Main(string[] args)
{
    del myDelegate = x => x * x;
    int j = myDelegate(5);
}
~~~
To create an expression tree type:
~~~
using System.Linq.Expressions;  

namespace ConsoleApplication1  
{  
    class Program  
    {  
        static void Main(string[] args)
        {
            Expression<del> myET = x => x * x;
        }
    }
}
~~~
The => operator has the same precedence as assignment (=) and is right associative (see "Associativity" section of the Operators article).

=> 연산자는 할당(=)과 우선순위가 같고 오른쪽 결합입니다.

Lambdas are used in method-based LINQ queries as arguments to standard query operator methods such as Where.

람다는 Where와 같은 표준 쿼리 연산자에 인수로 메서드 기반 LINQ 쿼리에서 사용됩니다.

When you use method-based syntax to call the Where method in the Enumerable class (as you do in LINQ to Objects and LINQ to XML) the parameter is a delegate type System.Func<T,TResult>. A lambda expression is the most convenient way to create that delegate. When you call the same method in, for example, the System.Linq.Queryable class (as you do in LINQ to SQL) then the parameter type is an System.Linq.Expressions.Expression<Func> where Func is any of the Func delegates with up to sixteen input parameters. Again, a lambda expression is just a very concise way to construct that expression tree. The lambdas allow the Where calls to look similar although in fact the type of object created from the lambda is different.

Enumerable 클래스에서 Where 메서드를 호출하기 위해 메서드 기반 문법을 사용하면 매개변수는 System.Func<T, TResult> 대리자 형식입니다. 람다 식은 대리자를 생성하기 위한 가장 편리한 바업ㅂ입니다. 예를 들어 System.Linq.Queryable 클래스에서 같은 메서드를 호출하면

In the previous example, notice that the delegate signature has one implicitly-typed input parameter of type int, and returns an int. The lambda expression can be converted to a delegate of that type because it also has one input parameter (x) and a return value that the compiler can implicitly convert to type int. (Type inference is discussed in more detail in the following sections.) When the delegate is invoked by using an input parameter of 5, it returns a result of 25.

Lambdas are not allowed on the left side of the is or as operator.

All restrictions that apply to anonymous methods also apply to lambda expressions. For more information, see Anonymous Methods.

