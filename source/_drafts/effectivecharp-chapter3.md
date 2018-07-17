---
title: effectivecharp-chapter3
tags:
---
## 아이템 20: IComparable\<T\>와 IComparer\<T\>을 이용하여 객체의 선후 관계를 정의하라
컬렉션을 정렬하거나 검색하려면 타입 내에 객체의 선후 관계를 판단할 수 있는 기능을 정의해야 한다. .NET framework는 객체의 선후 관계를 정의하기 위해서 IComparable\<T\>와 IComparer\<T\> 2개의 인터페이스를 제공한다. IComparable\<T\>는 타입의 기본적인 선후 관계를 정의한다. 그리고 IComparer\<T\>를 이용하면 기본적인 선후 관계 이외에 추가적인 선후 관계를 정의할 수 있다.

IComparable