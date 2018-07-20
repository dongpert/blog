---
title: netguide-tuple types
tags:
---
C# tuples are types that you define using a lightweight syntax. The advantages include a simpler syntax, rules for conversions based on number (referred to as cardinality) and types of elements, and consistent rules for copies, equality tests, and assignments. As a tradeoff, tuples do not support some of the object-oriented idioms associated with inheritance. You can get an overview in the section on tuples in the What's new in C# 7.0 article. 

C# 튜플은 가벼운 문법을 사용하여 정의하는 형식입니다. 장점은 가단한 문법, 숫자에 기반한 변환 규칙 및 요소의 형식, 그리고 사본, 동등 테스트, 할당에 대한 일관된 규칙입니다. 튜플을 상속과 관련된 객체 지향 관용구의 일부를 지원하지 않습니다. 

In this article, you'll learn the language rules governing tuples in C# 7.0 and later versions, different ways to use them, and initial guidance on working with tuples.

이 기사에서 C# 7.0 이후 버전에서 당신은 튜플을 관리하는 언어 규칙, 튜플을 사용하는 다양한 방법과 튜플을 사용한 작업에 대한 초기 지침을 학습 할 것입니다.

> The new tuples features require the ValueTuple types. You must add the NuGet package System.ValueTuple in order to use it on platforms that do not include the types.

> 새로운 튜플 특징은 ValueTuple 형식을 요구합니다. 형식이 포함되지 않은 플랫폼에서 사용하기 위해서는 Nuget package System.ValueType을 추가해야 합니다.

> This is similar to other language features that rely on types delivered in the framework. Examples include async and await relying on the INotifyCompletion interface, and LINQ relying on IEnumerable<T>. However, the delivery mechanism is changing as .NET is becoming more platform independent. The .NET Framework may not always ship on the same cadence as the language compiler. When new language features rely on new types, those types will be available as NuGet packages when the language features ship. As these new types get added to the .NET Standard API and delivered as part of the framework, the NuGet package requirement will be removed.

> 프레임워크에 전달되는 형식에 의존하는 다른언어 특징과 비슷합니다. 사례로 async 그리고 await는 INotifyCompletion 인터페이스에 의존하고, LINQ는 IEnumerable<T>에 의존합니다. 그러나 전달 메커니즘이 닷넷이 더 플랫폼에 독립적이 되면서 변하고 있습니다. 닷넷 프레임워크는 항상 언어 컴파일러와 같은 케이던스에 제공되는 것은 아닙니다. 새로운 언어 특징이 새로운 타입에 의존하면 이 형식은 Nuget 패키지로 이용될 수 있습니다. 새로운 형식이 닷넷 표준 APi에 추가되고 프레임워크의 일부로 전달되면 NuGet 패키지 요구는 제거될 것입니다.

