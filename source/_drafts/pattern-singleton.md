---
title: Implementing the Singleton Pattern in C#
tags:
---

## Introduction
 The singleton pattern is one of the best-known patterns in software engineering. Essentially, a singleton is a class which only allows a single instance of itself to be created, and usually gives simple access to that instance. Most commonly, singletons don't allow any parameters to be specified when creating the instance - as otherwise a second request for an instance but with a different parameter could be problematic! (If the same instance should be accessed for all requests with the same parameter, the factory pattern is more appropriate.) This article deals only with the situation where no parameters are required. Typically a requirement of singletons is that they are created lazily - i.e. that the instance isn't created until it is first needed.

 싱글톤 패턴은 소프트웨어 엔지니어링에서 가장 잘 알려진 패턴의 하나 입니다. 본질적으로 싱글톤은 자신의 단일 인스턴스만 생성되도록 허용하는 클래스 입니다. 그리고 보통 인스턴스에 대한 간단한 엑세스를 제공합니다. 가장 일반적으로 싱글톤은 인스턴스를 생성할 때 지정되는 어떤 파라미터도 허용하지 않습니다. 그렇지 않으면 인스턴스에 대한 두번째 요청이지만 다른 매개변수로 인해 문제가 될 수 있습니다.(동일한 매개변수를 사용하는 모든 요청에 대해 동일한 인스턴스에 접근해야 한다면 팩토리 패턴이 더 적합합니다.) 이 문서는 파라미터가 필요하지 않는 상황만 다룹니다. 일반적으로 싱글톤의 요구사항은 그들이 느슨하게 생성된다는 것입니다. 즉 인스턴스는 처음 필요하기 전까지 생성되지 않습니다.

There are various different ways of implementing the singleton pattern in C#. I shall present them here in reverse order of elegance, starting with the most commonly seen, which is not thread-safe, and working up to a fully lazily-loaded, thread-safe, simple and highly performant version.

C#에서 싱글톤을 구현하는 다양한 방법이 있습니다. 가장 일반적으로 보이는 것으로 시작하여 스레드로부터 안전하지 않으며 완전히 느슨하게 로드되고, 스레드로부터 안전하고 간단하고 고성능 버전까지 우아함의 역순으로 여기에 그들을 제시할 것입니다.

All these implementations share four common characteristics, however:

* A single constructor, which is private and parameterless. This prevents other classes from instantiating it (which would be a violation of the pattern). Note that it also prevents subclassing - if a singleton can be subclassed once, it can be subclassed twice, and if each of those subclasses can create an instance, the pattern is violated. The factory pattern can be used if you need a single instance of a base type, but the exact type isn't known until runtime.
* The class is sealed. This is unnecessary, strictly speaking, due to the above point, but may help the JIT to optimise things more.
* A static variable which holds a reference to the single created instance, if any.
* A public static means of getting the reference to the single created instance, creating one if necessary.

모든 구현은 네가지 공통된 특징을 공유합니다.
* private 그리고 파라미터가 없는 단일 생성자 입니다. 다른 클래스가 인스턴스를 생성하는것을 방지합니다. 서브클래스 하는것을 방지합니다. - 싱클톤이 한번 서브클래스한다면 서브클래스는 두번 될 수 있습니다. 그리고 서브클래스들 각각 인트턴스를 생성할 수 있습니다. 패턴이 위반됩니다.
* 클래스는 봉인되었습니다. 이것은 불필요합니다.
* 생성된 단일 인스터에 대한 참조를 보유하는 정적변수입니다.