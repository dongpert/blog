---
title: netapi-ienumerator-generic-interface
tags:
---
## Examples
The following example shows an implementation of the IEnumerator<T> interface for a collection class of custom objects. The custom object is an instance of the type Box, and the collection class is BoxCollection. This code example is part of a larger example provided for the ICollection<T> interface.
~~~
// Defines the enumerator for the Boxes collection.
// (Some prefer this class nested in the collection class.)
public class BoxEnumerator : IEnumerator<Box>
{
    private BoxCollection _collection;
    private int curIndex;
    private Box curBox;


    public BoxEnumerator(BoxCollection collection)
    {
        _collection = collection;
        curIndex = -1;
        curBox = default(Box);

    }

    public bool MoveNext()
    {
        //Avoids going beyond the end of the collection.
        if (++curIndex >= _collection.Count)
        {
            return false;
        }
        else
        {
            // Set current box to next item in collection.
            curBox = _collection[curIndex];
        }
        return true;
    }

    public void Reset() { curIndex = -1; }

    void IDisposable.Dispose() { }

    public Box Current
    {
        get { return curBox; }
    }


    object IEnumerator.Current
    {
        get { return Current; }
    }

}
~~~
## Remarks
IEnumerator\<T\> is the base interface for all generic enumerators.
IEnumerator\<T\>는 모든 제네릭 enumerators에 대한 기본 인터페이스입니다.

The foreach statement of the C# language (for each in C++, For Each in Visual Basic) hides the complexity of the enumerators. Therefore, using foreach is recommended, instead of directly manipulating the enumerator.

C# 언어의 foreach문은 enumerators의 복잡성을 감춥니다. 그러므로 enumerator를 직접 조작하는 대신에 foreach 사용하는것을 추천합니다.

Enumerators can be used to read the data in the collection, but they cannot be used to modify the underlying collection.

Enumerators는 컬렉션의 데이터를 읽을 때 사용될 수 있지만 근본적인 컬렉션을 수정하기 위해 사용될 수 없습니다.

Initially, the enumerator is positioned before the first element in the collection. At this position, Current is undefined. Therefore, you must call MoveNext to advance the enumerator to the first element of the collection before reading the value of Current.

처음에 enumerator는 컬렉션의 첫 요소 앞에 위치해 있습니다. 이 위치에서 Current는 undefined입니다. 그러므로 Current의 값을 읽기 전에 컬렉션에 첫요소에 enumerator를 이동시키기 위해서 MoveNext를 호출해야 합니다.
Current returns the same object until MoveNext is called. MoveNext sets Current to the next element.

If MoveNext passes the end of the collection, the enumerator is positioned after the last element in the collection and MoveNext returns false. When the enumerator is at this position, subsequent calls to MoveNext also return false. If the last call to MoveNext returned false, Current is undefined. You cannot set Current to the first element of the collection again; you must create a new enumerator instance instead.

MoveNext 컬렉션의 끝으로 이동하면 enumerator는 컬렉션의 마지막 요소 뒤에 위치하고 MoveNext는 false를 반환합니다. Current는 undefined입니다. Currnet를 다시 컬렉션의 첫 요소로 설정할 수 없습니다. 대신에 새로운 enumerator 인스턴스를 생성해야 합니다.

The Reset method is provided for COM interoperability. It does not necessarily need to be implemented; instead, the implementer can simply throw a NotSupportedException. However, if you choose to do this, you should make sure no callers are relying on the Reset functionality.

Reset 메서드는 COM 상호운용성을 위해 제공됩니다. 반드시 구현되어야 할 필요는 없습니다 대신 구현자는 단순히 NotSupportedException을 던질 수 있습니다. 그러나 이렇게 하기로 했다면 Reset 기능에 의존하는 호출자가 없는지 확인해야만 합니다.

If changes are made to the collection, such as adding, modifying, or deleting elements, the behavior of the enumerator is undefined.

컬렉션이 변경이 있다면 enumerator의 동작은 undefined 됩니다.

The enumerator does not have exclusive access to the collection; therefore, enumerating through a collection is intrinsically not a thread-safe procedure. To guarantee thread safety during enumeration, you can lock the collection during the entire enumeration. To allow the collection to be accessed by multiple threads for reading and writing, you must implement your own synchronization.

enumerator는 컬렉션에 대한 배타적 접근을 할 수 없습니다. 그러므로 컬렉션을 통한 열거는 본질적으로 쓰레드에 안전한 방법이 아닙니다. 열거하는 동안 쓰레드 안정성을 보장하기 위해서 전체 열거동안 컬렉션을 잠글 수 있습니다. 읽고 작성 하는것에 대해 여러개의 쓰레드가 접근하는 컬렉션을 허용하기 위해 자체 동기화를 구현해야 합니다.

Default implementations of collections in the System.Collections.Generic namespace are not synchronized.

## Notes to Implementers
Implementing this interface requires implementing the nongeneric IEnumerator interface. The MoveNext() and Reset() methods do not depend on T, and appear only on the nongeneric interface. The Current property appears on both interfaces, and has different return types. Implement the nongeneric Current property as an explicit interface implementation. This allows any consumer of the nongeneric interface to consume the generic interface.

인터페이스를 구현하는 것은 비제네릭 IEnumerator 인터페이스 구현을 필요로 합니다. MoveNext() 와 Reset() 메서드는 T에 의존하지 않고 비제네릭 인터페이스에서만 나타납니다. Current 속성은 각 인터페이스에 나타나고 다른 반환 형식을 갖고 있습니다.

In addition, IEnumerator<T> implements IDisposable, which requires you to implement the Dispose() method. This enables you to close database connections or release file handles or similar operations when using other resources. If there are no additional resources to dispose of, provide an empty Dispose() implementation.