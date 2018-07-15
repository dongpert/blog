---
title: NetGuide-Event
tags:
---
## Handling and Raising Events
Events in the .NET Framework are based on the delegate model. The delegate model follows the observer design pattern, which enables a subscriber to register with, and receive notifications from, a provider. An event sender pushes a notification that an event has happened, and an event receiver receives that notification and defines a response to it. This article describes the major components of the delegate model, how to consume events in applications, and how to implement events in your code.

닷넷 프레임워크에서 이벤트는 델리게이트 모델을 기반으로 합니다. 델리게이트 모델은 구독자가 공급자에 등록하고 알림을 받을 수 있도록 하는 관찰자 디자인 패턴을 따릅니다. 이벤트 송신자는 이벤트가 발생했다는 알림을 보내고 이벤트 수신자는 통지를 수신하고 응답을 정의합니다. 이 기사는 델리게이트 모델의 주요한 컴포넌트, 애플리케이션에서 이벤트를 소비하는 방법, 당신의 코드에서 이벤트를 구현하는 방법을 설명합니다.

For information about handling events in Windows 8.x Store apps, see Events and routed events overview.

### Events
An event is a message sent by an object to signal the occurrence of an action. The action could be caused by user interaction, such as a button click, or it could be raised by some other program logic, such as changing a property’s value. **The object that raises the event is called the event sender.** The event sender doesn't know which object or method will receive (handle) the events it raises. **The event is typically a member of the event sender**; for example, the Click event is a member of the Button class, and the PropertyChanged event is a member of the class that implements the INotifyPropertyChanged interface.

이벤트는 동작의 발생을 알리기 위해 객체가 보내는 메세지 입니다. 동작은 버튼클릭과 같은 사용자 상호작용에 의해 발생되거나 속성의 값이 변경되는 것과 같은 다른 프로그램 논리에 의해 발생될 수 있습니다. 이벤트를 발생시키는 객체는 이벤트 송신자라고 합니다. 이벤트 송신자는 발생시키는 이벤트를 수신(처리)할 객체나 메서드를 알지 못합니다. 이벤트는 보통 이벤트 전송자의 멤버입니다. 예를 들어 Click 이벤트는 Button 클래스의 멤버이고, PropertyChanged 이벤트는 INotityPropertyChange 인터페이스를 구현하는 클래스의 멤버입니다.

To define an event, you use the event (in C#) or Event (in Visual Basic) keyword in the signature of your event class, and specify the type of delegate for the event. Delegates are described in the next section.

이벤트를 정의하기 위해 당신의 이벤트 클래스의 시그니처에서 event 또는 Event 키워드를 사용하고 이벤트를 대한 델리게이트의 형식을 지정합니다. 델리게이트는 다음 부분에서 설명합니다.

Typically, to raise an event, you add a method that is marked as protected and virtual (in C#) or Protected and Overridable (in Visual Basic). Name this method OnEventName; for example, OnDataReceived. The method should take one parameter that specifies an event data object. You provide this method to enable derived classes to override the logic for raising the event. A derived class should always call the OnEventName method of the base class to ensure that registered delegates receive the event.

보통 이벤트를 일으키기 위해서 당신은 protected와 virtual로 표기된 메서드를 추가합니다. 이 메서드의 이름을 OnEventName으로 지정하십시요. 예를 들어 OnDataReceived. 메서드는 이벤트 데이터 객체를 지정하는 한개의 매개변수를 가져와야 합니다. 당신은 파생된 클래스가 이벤트를 발생시키기 위한 논리를 재정의할 수 있게 하도록 이 메서드를 제공합니다. 파생된 클래스는 항상 등록된 델리게이트가 이벤트를 수신받을 수 있게 기본 클래스의 OnEvnetName 메서드로 호출해야 합니다.

The following example shows how to declare an event named ThresholdReached. The event is associated with the EventHandler delegate and raised in a method named OnThresholdReached.

다음 예제에서는 ThresholdReached라는 이벤트를 선언하는 방법을 보여줍니다. 이 이벤트는 EventHandler 델리게이트와 관련이 있고 OnThresholdReached라는 메서드에서 발생합니다.
~~~
class Counter
{
    // event sender
    public event EventHandler ThresholdReadched;

    protected virtual void OnThresholdReadched(EventArgs e)
    {
        EventHandler handler = ThresholdReadched;
        if (handler != null)
        {
            handler(this, e);
        }
    }
}
~~~

### Delegates
A delegate is a type that holds a reference to a method. A delegate is declared with a signature that shows the return type and parameters for the methods it references, and can hold references only to methods that match its signature. A delegate is thus equivalent to a type-safe function pointer or a callback. A delegate declaration is sufficient to define a delegate class.

델리게이트는 메서드에 대한 참조를 가지는 형식입니다. 델리게이트는 참조하고 있는 메서드에 대한 반환 형식과 매개변수를 보여주는 시그니처로 선언되고 시그니처가 일치하는 메서드에 대해서만 참조를 가질수 있습니다. 델리게이트는 그러므로 형식이 안전한 함수 포인터 또는 콜백과 같습니다. 델리게이트 선언은 델리게이트 클래스를 정의하기에 충분합니다.

Delegates have many uses in the .NET Framework. In the context of events, a delegate is an intermediary (or pointer-like mechanism) between the event source and the code that handles the event. You associate a delegate with an event by including the delegate type in the event declaration, as shown in the example in the previous section. For more information about delegates, see the Delegate class.

델리게이트는 닷넷 프레임워크에서 많은 용도로 사용됩니다. 이벤트 컨텍스트에서 델리게이트는 이벤트 소스와 이벤트를 다루는 코드의 중재자 입니다. 이전 부분에 예제에서 처럼 당신은 이벤트 선언에 델리게이트 형식을 포함하여 델리게이트와 이벤트를 연결합니다.

The .NET Framework provides the EventHandler and EventHandler\<TEventArgs\> delegates to support most event scenarios. Use the EventHandler delegate for all events that do not include event data. Use the EventHandler\<TEventArgs\> delegate for events that include data about the event. These delegates have no return type value and take two parameters (an object for the source of the event and an object for event data).

닷넷 프레임워크는 대부분 이벤트 시나리오를 지원하기 위해 EventHandler와 EventHandelr\<TEventArgs\> 델리게이트를 제공합니다. 이벤트 데이터를 포함하지 않는 모든 이벤트에 대해 EventHandler 델리게이트를 사용합니다. 이벤트에 관한 데이터를 포함하는 이벤트에 대해 EventHandler<TEventArgs> 델리게이르르 사용합니다. 이러한 델리게이트는 값을 반환 형식 값이 없고 두개의 매개변수를 취합니다.

Delegates are multicast, which means that they can hold references to more than one event-handling method. For details, see the Delegate reference page. Delegates provide flexibility and fine-grained control in event handling. A delegate acts as an event dispatcher for the class that raises the event by maintaining a list of registered event handlers for the event.

델리게이트는 하나의 이벤트 처리 메서드 보다 많은 참조를 가질수 있는 멀티캐스트입니다. 델리게이트는 유연성과 이벤트 처리에 세부 제어를 제공합니다 델리게이트는 이벤트에 대한 등록된 이벤트 핸들러의 리스트를 유지하여 이벤트를 발생시키는 클래스에 대해 이벤트 dispatcher처럼 작동합니다.

For scenarios where the EventHandler and EventHandler<TEventArgs> delegates do not work, you can define a delegate. Scenarios that require you to define a delegate are very rare, such as when you must work with code that does not recognize generics. You mark a delegate with the delegate in (C#) and Delegate (in Visual Basic) keyword in the declaration. The following example shows how to declare a delegate named ThresholdReachedEventHandler.

EventHandler와 EventHandler\<TEventArgs\> 델리게이트가 작동하지 않는 시나리오의 경우 델리게이트르 정의할 수 있습니다. 제네릭을 인식하지 않는 코드로 작업을 해야 하는 경우 처럼 당신이 델리게이트를 정의하기를 필요로 하는 시나리오는 매우 드뭅니다. 다음 예제는 ThresholdReachedEventHandler 이름의 델리게이트를 선언하는 방법을 보여줍니다.
~~~
public delegate void ThresholdReachedEventHandler(object sender, ThresholdReachedEventArgs e);
~~~

### Event Data
Data that is associated with an event can be provided through an event data class. The .NET Framework provides many event data classes that you can use in your applications. For example, the SerialDataReceivedEventArgs class is the event data class for the SerialPort.DataReceived event. The .NET Framework follows a naming pattern of ending all event data classes with EventArgs. You determine which event data class is associated with an event by looking at the delegate for the event. For example, the SerialDataReceivedEventHandler delegate includes the SerialDataReceivedEventArgs class as one of its parameters.

이벤트와 관련이 있는 데이터는 이벤트 데이터 클래스를 통해 제공될 수 있습니다. 닷넷 프레임워크는 어플리케이션에서 사용할 수 있는 많은 데이터 이벤트 클래스를 제공합니다. 예를 들어 SerialDataReceivedEventArgs 클래스는 SerialPort.DataReceived를 위한 이벤트 데이터 클래스 입니다. 닷넷 프레임워크는 모든 이벤트 데이터 클래스를 EventArgs로 종료하는 명명 패턴을 따릅니다. 당신은 이벤트에 대한 델리게이트를 조사하여 이벤트 이벤트와 관련된 데이터 클래스를 알아낼 수 있습니다. 예를 들어 SerialDataReceivedEventHandler 델리게이트는 매개변수의 하나로 SerialDataReceivedEventArgs 클래스를 포함합니다.

The EventArgs class is the base type for all event data classes. EventArgs is also the class you use when an event does not have any data associated with it. When you create an event that is only meant to notify other classes that something happened and does not need to pass any data, include the EventArgs class as the second parameter in the delegate. You can pass the EventArgs.Empty value when no data is provided. The EventHandler delegate includes the EventArgs class as a parameter.

EventArgs 클래스는 모든 이벤트 데이터 클래스에 기본 타입입니다. EventArgs는 또한 이벤트가 관련된 데이터를 가지고 있지 않을때 사용하는 클래스입니다. 다른 클래스에 어떤일이 발생했고 데이터를 전달할 필요가 없다는 것을 알리기만 하는 이벤트를 생성할때 델리게이트에 두번째 매개변수로 EventArgs 클래스를 포함시킵니다. 데이터가 제공되지 않았을때 EventArgs.Empty 값을 전달할 수 있습니다. EventHandler 델리게이트는 매개변수로 EventArgs 클래스를 포함시킵니다.

When you want to create a customized event data class, create a class that derives from EventArgs, and then provide any members needed to pass data that is related to the event. Typically, you should use the same naming pattern as the .NET Framework and end your event data class name with EventArgs.

사용자 지정 이벤트 데이터 클래스를 생성하기 원한다면 EventArgs에서 파생되는 클래스를 생성하고, 다음 이벤트와 관련이 있는 데이터를 전달하는데 필요한 멤버들을 제공하십시오. 보통 닷넷 프레임워크처럼 같은 명명 패턴을 사용하고 당신의 이벤트 데이터 클래스 이름을 EventArgs로 끝내야 합니다.

The following example shows an event data class named ThresholdReachedEventArgs. It contains properties that are specific to the event being raised.

다음 예제는 ThresholdReachedEventArgs 이름의 이벤트 데이터 클래스 보여줍니다.

~~~
public class ThresholdReachedEventArgs : EventArgs
{
    public int Threhold { get; set; }
    public DateTime TimeReached { get; set; }
}
~~~

### Event Handlers
To respond to an event, you define an event handler method in the event receiver. This method must match the signature of the delegate for the event you are handling. In the event handler, you perform the actions that are required when the event is raised, such as collecting user input after the user clicks a button. To receive notifications when the event occurs, your event handler method must subscribe to the event.

이벤트에 응답하기 위해 당신은 이벤트 수신자에 이벤트 핸들러 메서드를 정의합니다. 이 메서드는 다루고 있는 이벤트에 대한 델리게이트의 시그니처와 일치해야 합니다. 사용자가 버튼을 클릭한 후 사용자 입력을 수집하는 것과 같은 이벤트 핸들러에서 당신은 이벤트가 발생했을때 필요로 하는 동작을 수행합니다. 이벤트가 발생했을때 알림을 수신하기 위해 이벤트 핸들러 매서드는 이벤트를 구독해야 합니다.

The following example shows an event handler method named c_ThresholdReached that matches the signature for the EventHandler delegate. The method subscribes to the ThresholdReached event.

다음 예제는 EventHandler 델리게이트에 대한 시그니처가 일치하는 c_ThresholdReached 이름의 이벤트 핸들러 메서드를 보여줍니다. 메서드는 ThresholdReached 이벤트를 구독합니다.
~~~
class Program
{
    static void Main(string[] args)
    {
        Counter c = new Counter();
        c.ThresholdReadched += c_ThresholdReached;
    }

    static void c_ThresholdReached(object sender, EventArgs e)
    {
        Console.WriteLine("The threshold was reached");
    }
}
~~~

## Static and Dynamic Event Handlers
The .NET Framework allows subscribers to register for event notifications either statically or dynamically. Static event handlers are in effect for the entire life of the class whose events they handle. Dynamic event handlers are explicitly activated and deactivated during program execution, usually in response to some conditional program logic. For example, they can be used if event notifications are needed only under certain conditions or if an application provides multiple event handlers and run-time conditions define the appropriate one to use. The example in the previous section shows how to dynamically add an event handler. For more information, see Events and Events.

닷넷 프레임워크는 구독자가 정적이나 동적으로 이벤트 알림을 등록하는 것을 허용합니다. 정적 이벤트 핸들러는 이벤트를 처리하는 클래스 전체 수명동안 유효합니다. 동적 이벤트 핸들러는 프로그램이 실행되는 동안되고 보통 일부 조건부 프로그램 논리에 응답으로 명시적으로 활성화되고 비활성화됩니다. 예를 들어 이벤트 알림이 특정한 조건에서만 필요하거나 애플리케이션이 여러 이벤트 핸들러를 제공하고 런타임 조건이 적절히 사용할 이벤트 알림을 정의하면 사용될 수 있습니다. 이전 부분 예제는 동적으로 이벤트 핸들러를 추가하는 정보를 보여줍니다. 

## Raising Multiple Events
If your class raises multiple events, the compiler generates one field per event delegate instance. If the number of events is large, the storage cost of one field per delegate may not be acceptable. For those situations, the .NET Framework provides event properties that you can use with another data structure of your choice to store event delegates.

당신의 클래스가 여러개의 이벤트를 발생시키면 컴파일러는 이벤트 델리게이트 인스턴스마다 하나의 필드를 생성합니다. 이벤트의 수가 크면 델리게이트마다 하나의 저장 비용이 허용되지 않습니다. 이러한 상황의 경우 닷넷 프레임워크는 이벤트 델리게이트를 저장하기 위해 선택한 다른 데이터 구조체와 함께 사용할 수 있는 이벤트 속성들을 제공합니다.

Event properties consist of event declarations accompanied by event accessors. Event accessors are methods that you define to add or remove event delegate instances from the storage data structure. Note that event properties are slower than event fields, because each event delegate must be retrieved before it can be invoked. The trade-off is between memory and speed. If your class defines many events that are infrequently raised, you will want to implement event properties. For more information, see How to: Handle Multiple Events Using Event Properties.

이벤트 속성은 이벤트 접근자를 동반하는 이벤트 선언으로 구성됩니다. 이벤트 접근자는 저장 데이터 구조에서 이벤트 델리게이트 인스턴스를 저장하거나 제거하기 위해 정의하는 메서드입니다. 각 이벤트 델리게이트는 호출되기 전에 검색되야 하기 때문에 이벤트 소석은 이벤트 필드보다 느립니다. 클래스가 자주 발생하는 많은 이벤트를 정의하면 이벤트 속석을 구현하기를 바랄것 입니다. 더 많은 정보는 이벤트 속성을 사용하는 여러개의 이벤트 처리를 참조하십시오.