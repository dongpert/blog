---
title: what-is-a-unit-test
tags:
---
Unit Testing is the idea of writing additional code, called test code, for the purpose of testing the smallest blocks of production code to the full extent possible to make sure those blocks of code are error free.

단위 테스트는 전체 범위에 코드 블록이 오류가 없는지 확인하기 위해 생산 코드의 가장 작은 블록을 테스트할 목적으로 테스트 코드라고 하는 추가적인 코드를 작성하는 개념입니다.

Code is usually designed into Classes or Objects. The term “Class” and the term “Object” are usually used interchangeably. Classes are made up of variables and functions that include Constructors, Methods, and Properties but they may also include sub-classes.

코드는 일반적으로 클래스 또는 개체로 설계됩니다. 클래스라는 용어와 객체라는 용어는 일반적으로 바꿔가면서 사용됩니다. 클래스는 변수와 생성자, 메서드, 속성을 포함하는 함수로 구성됩니다. 그러나 서브클래스 역시 포함할 수 있습니다.

For each Object in code, you should have a Unit Test for that object. For each method in an Object, the correlating Unit Test should have a test. In fact, a test should exist that makes sure that every line of your code functions as expected.

코드에서 각 객체에 대해 당신은 객체에 대한 단위테스트를 해야만 힙니다. 객체에서 각 메서드에 대해 연관 단위 테스트는 테스트가 있어야 합니다. 실제로 테스트는 당신의 코드 모든 줄이 예상대로 작동하는지 확인하는 것이 있어야 합니다.

## Code Coverage
Unit Tests are about making sure each line of code functions properly. A Unit Test will execute lines of codes. Imagine you have 100 lines of code and a Unit Test tests only 50 lines of code. You only have 50% code coverage. The other 50% of you code is untested.

단위 테스트는 코드읭 각 줄이 제대로 작동하는지 확인하는 것 입니다. 단위 테스트는 코드의 줄을 실행할 것입니다. 100줄의 코드가 있고 단위 테스트는 50줄의 코드만 테스트한다고 상상해 보십시오. 당신은 50퍼센트의 코드 커버리지만 가지고 있습니다. 당신의 코드 50퍼센트는 테스트 되지 않았습니다.

Most Unit Test tools, MSTest, MBUnit, NUnit, and others can provide reports on code coverage.

대부분의 단위 테스트 도구인 MSTest, MBUnit, NUnit 및 기타는 코드 커버리지에 보고서를 제공할 수 있습니다.

## Cyclomatic Complexity
Cyclomatic Complexity is a measurement of how complex your code is. This usually comes up in Unit Testing because in order to get 100% code coverage, one has to tests every possible path of code. The more your code branches, the more complex the code is.

Cyclomatic Complexity는 당신의 코드가 얼마나 복잡한지 측정합니다. 일반적으로 100퍼센트의 코드 커버리지를 얻기 위해 코드의 모든 가능한 경로를 테스트 해야 되기 때문에 단위테스트에서 문제가 발생합니다.

If you have to write twenty tests just to get 100% coverage for a single function, you can bet your cyclomatic complexity is too high and you should probably break the function up or reconsider the design.

단일 함수에 대해 100퍼센트 커버리지를 얻기 위해 20개의 테스트를 작성해야 한다면 cyclomatic complexity가 너무 높다는 것이 확실하고 아마도 함수를 분해하거나 설계를 재고해야 합니다.

## Parameter Value Coverage
Parameter Value Coverage is the idea of checking both expected and unexpected values for a given type. Often bugs occur because testing is not done on an a type that occurs unexpectedly at run time. Often a value is unexpectedly null and null was not handled, causing the infamous null reference exception.

Parameter Value Coverage는 주어진 유형에 대해 예상값과 예기치 않는 값을 모두 확인하는 개념입니다. 테스트가 런타임에 예기치 않게 발생하는 유형에서 작동하지 않기 때문에 종종 버그가 발생합니다. 종종 값이 예기치 않게 널이고 널은 처리되지 않아 악명높은 널 참조 예외를 초래합니다.

Look at this function. Can you see the bug?
~~~
public bool CompareStrings(String inStr1, String inStr2)
{
    return inStr1.Equals(inStr2);
}
~~~
A System.NullReferenceExecption will occur if inStr1 is null.

inStr1이 null이면 System.NullReferenceException이 발생할 것입니다.

How can we find these bugs before a customer reports them? A unit test can catch these bugs. We can make lists of anything we can think of that might react differently.

고객이 보고하기 전에 어떻게 이런 버그들을 발견할 수 있을까? 단위 테스트는 이러한 버그들을 잡을 수 있습니다. 우리는 다르게 반응할 수 있는 것으로 생각할 수 있는 것을 목록으로 만들 수 있습니다.

For example, imagine a function that takes a string. What should we try to test:

1. An actual string: "SomeText";
2. An uninitialized string: null;
3. A blank string: ""
4. A string with only whitespace: "   ";

예를 들어 문자열을 취하는 함수를 상상해 보십시오. 우리는 어떤 테스트를 해야 하는가:

1. 실제 문자열: "SomeText";
2. 초기화되지 않은 문자열: null;
3. 빈 문자열: "",
4. 공백만 있는 문자열: "    ";

Now imaging a function takes an object called PersonName that has a string member for FirstName and LastName value. We should try to test some of the above.

FirstName과 LastName값에 대한 string 멤버를 가지는 PersonName이라는 객체를 취하는 함수를 상상해 보십시오.

1. A PersonObject but nothing populated: new PersonName();
2. A PersonObject but everything populated: new PersonName() {FirstName=”John”, LastName=”Johnson”};
3. A PersonObject but partially populated: new PersonName() {LastName=”Johnson”} || new PersonName() {FirstName=”John”};
4. An uninitialized PersonName object: null;

Here is a test that will check for null in the compare strings code.

비교하는 문자열 코드에서 null을 검사할 수 있는 테스트가 있습니다.

Note: There is a decision to make here. If comparing two string objects and both are null, do you want to return true, because both are null so they match? Or do you want to return false, because neither are even strings? For the code below, I’ll assume two null strings should not be considered equal.
~~~
public bool CompareStrings_Test()
{
    String test1 = null;
    String test2 = null;
    bool expected = false;
    bool actual = CompareStrings(test1, test2);
    Assert.AreEqual(expected, actual);
}
~~~