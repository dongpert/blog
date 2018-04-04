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

## Third version - attempted thread-safety using double-check locking
~~~
// Bad code! Do not use!
public sealed class Singleton
{
    private static Singleton instance = null;
    private static readonly object padlock = new object();

    Singleton()
    {
    }

    public static Singleton Instance
    {
        get
        {
            if (instance == null)
            {
                lock (padlock)
                {
                    if (instance == null)
                    {
                        instance = new Singleton();
                    }
                }
            }
            return instance;
        }
    }
} 
~~~

This implementation attempts to be thread-safe without the necessity of taking out a lock every time. Unfortunately, there are four downsides to the pattern:

이 구현은 매번 잠금을 해제할 필요없이 스레드로부터 안전하려고 합니다. 불행하게도 4가지의 단점이 있습니다.

* It doesn't work in Java. This may seem an odd thing to **comment on**, but it's worth knowing if you ever need the singleton pattern in Java, and C# programmers may well also be Java programmers. The Java memory model doesn't ensure that the constructor completes before the reference to the new object is assigned to instance. The Java memory model **underwent** a reworking for version 1.5, but double-check locking is still broken after this without a volatile variable (as in C#).
* Without any memory barriers, it's broken in the ECMA CLI specification too. It's possible that under the .NET 2.0 memory model (which is stronger than the ECMA spec) it's safe, but I'd rather not rely on those stronger semantics, especially if there's any doubt as to the safety. Making the instance variable volatile can make it work, as would explicit memory barrier calls, although in the latter case even experts can't agree exactly which barriers are required. I tend to try to avoid situations where experts don't agree what's right and what's wrong!
* It's easy to get wrong. The pattern needs to be pretty much exactly as above - any significant changes are likely to impact either performance or correctness.
* It still doesn't perform as well as the later implementations.

* 자바에서 작동하지 않습니다. 이것은 논평하는 것이 이상한 것처럼 보일지 모르지만 가치있는 지식입니다. 자바에서 싱글톤 패턴이 필요하다면 C# 프로그램머 역시 자바 프로그래머 일지 모릅니다. 자바 메모리 모델은 인스턴스에 할당되는 새객체를 참조하기 전에 생성자가 완료하는것을 보장하지 않습니다. 자바 메모리 모델은 1.5 버전에 대한 재작업을 받았지만 이후에도 휘방성 변수 없는 더블체크 잠금은 여전히 
손상되었습니다.
* 
* 잘못하기 쉽습니다. 패턴은 위화 거의 정확해야 합니다. 어떤 중요한 변경은 성능이나 정확성에 영향을 끼치기 쉽습니다.
* 그것은 후에 구현 뿐만 아니라 여전히 수행하지 못합니다.


## Fourth version - not quite as lazy, but thread-safe without using locks
~~~
public sealed class Singleton
{
    private static readonly Singleton instance = new Singleton();

    // Explicit static constructor to tell C# compiler
    // not to mark type as beforefieldinit
    static Singleton()
    {
    }

    private Singleton()
    {
    }

    public static Singleton Instance
    {
        get
        {
            return instance;
        }
    }
} 
~~~

 As you can see, this is really is extremely simple - but why is it thread-safe and how lazy is it? Well, static constructors in C# are specified to execute only when an instance of the class is created or a static member is referenced, and to execute only once per AppDomain. Given that this check for the type being newly constructed needs to be executed whatever else happens, it will be faster than adding extra checking as in the previous examples. There are a couple of wrinkles, however:
 
보시다시피 실제는 극히 간단합니다 - 그러나 이것이 왜 스레드로부터 안전하고 게으른 방법입니까? C#에서 정적 생성자는 클래스의 인스턴스가 생성되거나 정적 멤버가 참조될때만 실행하도록 지정되고. 앱도메인 당 한번만 실행하도록 지정됩니다. 새로 생성된 유형에 대한 검사가 어떤 일이 있어도 실행되어야 하는것을 고려하면 이전 예제처럼 추가 검사를 추가하는 것보다 빠를 것입니다.

* It's not as lazy as the other implementations. In particular, if you have static members other than Instance, the first reference to those members will involve creating the instance. This is corrected in the next implementation.
* There are complications if one static constructor invokes another which invokes the first again. Look in the .NET specifications (currently section 9.5.3 of partition II) for more details about the exact nature of type initializers - they're unlikely to bite you, but it's worth being aware of the consequences of static constructors which refer to each other in a cycle.
* The laziness of type initializers is only guaranteed by .NET when the type isn't marked with a special flag called beforefieldinit. Unfortunately, the C# compiler (as provided in the .NET 1.1 runtime, at least) marks all types which don't have a static constructor (i.e. a block which looks like a constructor but is marked static) as beforefieldinit. I now have an article with more details about this issue. Also note that it affects performance, as discussed near the bottom of the page.

* 다른 구현처럼 지연되지 않습니다. 특히 인스턴스가 아닌 다른 정적멤버가 있다면 해당 멤버에 대한 참조는 인스턴스 생성이 포함됩니다. 이것은 다음 구현에서 수정됩니다.
* 한 정적 생성자가 첫 번째 정적 생성자를 호출하는 다른 생성자를 호출하면 복잡합니다. 형식 초기화의 정확한 특성에 관한 자세한 내용은 닷넷 사양을 살펴보십시오.
* 형식 이니셜라이저의 지연은 형식은 beforefieldinit라는 특수 플래그로 표시되지 않는 경우 닷넷에서만 보장됩니다. 불행히도 C# 컴파일러는 정적 생성자(생성자와 비슷하지만 정적으로 표시되는 블록)를 beforefieldinit로 모든 형식을 표시합니다. 이 문제에 대한 자세한 정보가 있는 기사가 있습니다. 페이지 하단 근처에서 논의한대로 성능에 영향을 미치는것에 주목하십시오

One **shortcut** you can take with this implementation (and only this one) is to just make instance a public static readonly variable, and **get rid of** the property entirely. This makes the basic skeleton code absolutely tiny! Many people, however, prefer to have a property **in case** further action is needed in future, and JIT inlining is likely to make the performance identical. (Note that the static constructor itself is still required if you require laziness.)

이 구현을 취할 수 있는 하나의 지름길은 인스턴스를 public static readonly 변수로 만들고 속성을 완전히 제거하는 것입니다. 이것은 기본적인 골격 코드를 절대적으로 작게 만듭니다. 그러나 많은 사람들이 추후조치가 필요한 경우에 대비하여 속성을 갖는것을 선호하고 JIT 인라인은 성능을 동일하게 만듭니다.

## Fifth version - fully lazy instantiation
~~~
public sealed class Singleton
{
    private Singleton()
    {
    }

    public static Singleton Instance { get { return Nested.instance; } }
        
    private class Nested
    {
        // Explicit static constructor to tell C# compiler
        // not to mark type as beforefieldinit
        static Nested()
        {
        }

        internal static readonly Singleton instance = new Singleton();
    }
} 
~~~

Here, instantiation is triggered by the first reference to the static member of the nested class, which only occurs in Instance. This means the implementation is fully lazy, but has all the performance benefits of the previous ones. Note that although nested classes have access to the enclosing class's private members, the reverse is not true, hence the need for instance to be internal here. That doesn't raise any other problems, though, as the class itself is private. The code is a bit more complicated in order to make the instantiation lazy, however. 

여기서 인스턴스화는 인스턴스에서만 발생하는 중첩 클래스의 정적멤버에 대한 첫번째 참조에 의해 트리거 됩니다. 이것은 구현이 완전히 지연되지만 이전것보다 모든 성능 이점을 가지고 있음을 의미합니다. 비록 중첩된 클래스가 앤클로징 클래스의 private 멤버에 접근할 수 있지만 반대는 사실이 아니므로 인스턴스는 내부에 있어야 합니다. 클래스 자체가 비공개이므로 어떤 다른 문제가 일어나지 않습니다. 코드가 인스턴스화를 지연시키기 위해서는 약간더 복잡합니다.