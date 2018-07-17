---
title: netapi-ienumerable-interface
tags:
---
## Examples
The following code example demonstrates the best practice for iterating a custom collection by implementing the IEnumerable and IEnumerator interfaces. In this example, members of these interfaces are not explicitly called, but they are implemented to support the use of foreach (For Each in Visual Basic) to iterate through the collection. This example is a complete Console app. To compile the Visual Basic app, change the Startup object to Sub Main in the project’s Properties page.

다음 코드 예제는 최선의 IEnumerable 과 IEnumerator 인터페이스를 구현하여 사용자 지정 컬렉션을 반복하기 위한 모범 사례를 보여줍니다. 이 예제에서 인터페이스의 멤버는 분명하게 호출되지 않지만 컬렉션을 통하여 반복하는 foreach의 사용을 지원하기 위해 구현됩니다. 이 예제는 완전한 콜솔 앱입니다. 

For a sample that shows how to implement the IEnumerable interface, see Implementing the IEnumerable Interface in a Collection Class

