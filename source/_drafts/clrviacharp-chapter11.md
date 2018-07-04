---
title: 'clrviacharp-chapter11 '
tags:
---
CLR의 이벤트 모델은 **델리게이트**에 기반을 두고 있다. 델리게이트는 타입 안정성을 유지한 채로 콜백 메서드를 호출하기 위한 방법이다. 콜백 메서드는 이벤트를 수신하려는 객체가 이벤트가 발생했을 알아내기 위해 사용된다.

### 이벤트를 노출하는 타입을 설계하기
#### 1단계: 이벤트 통지 과정에서 수신자들에게 보내야 하는 추가적인 정보들을 포함하는 타입을 정의하기
이벤트가 발생하면 이벤트를 발생시킨 객체는 이벤트를 수신하는 쪽으로 추가적인 정보들을 전달하기 원할 것이다. 이러한 추가적인 정보들은 단일 클래스로 캡슐화되어야 하는데, 보통은 private 필드와 이들 필드를 읽을 수만 있는 읽기 전용 속성들을 포함한다. 편의상 이벤트에 대한 추가 정보를 포함하는 클래스는 System.EventArgs 타입을 상속한 타입으로 정의하여 이벤트 핸들러로 전달하는 것이 일반적이다.
~~~
internal class NewMailEventArgs : EventArgs {
    private readonly String m_from, m_to, m_subject;

    public NewMailEventArgs(String from, String to, String subject) {
        m_from = from; m_to = to; m_subject = subject;
    }

    public String From { get { return m_from; }}
    public String To { get { return to; }}
    public String Subject { get { return Subject; }}
}
~~~

#### 2단계: 이벤트 멤버 정의하기
이벤트 멤버를 정의하기 위해서는 C#의 event 키워드를 사용하면 된다. 각각의 이벤트는 접근 가능 범위, 호출할 콜백 메서드의 프로토타입을 나타내는 델리게이트, 이벤트의 이름 순서로 정의한다.
~~~
internal class MailMember {
    public event EventHandler<NewMailEventArgs> NewMail;
}
~~~

#### 3단계: 등록된 객체에 이벤트가 발생하였음을 통지하기 위해서 이벤트를 발생시키는 메서드 정의하기
이벤트 사용의 편의를 위해서, 이벤트를 정의한 클래스나 이를 상속한 클래스 내에 이벤트를 발생시키는 메서드를 protected의 가상 메서드로 정의해두는 것이 좋다. 이 메서드는 일반적으로 이벤트 통지를 수신하는 객체에 전달할 NewMailEventArgs 객체만을 매개변수로 취하도록 정의한다. 이 메서드의 기본적인 구현 방식은 이벤트 수신을 등록한 객체가 있는지를 확인하여 등록된 모든 콜백 메서드를 호출해 주는 것이다.
~~~
internal class MailManager {
    ...
    protected virtual void OnNewMail(NewMailEventArgs e) {
        // 스레드 안정성을 위해 델리게이트 필드의 참조를 임시 필드로 복사한 후 작업을 수행한다.
        EventHandler<NewMailEventArgs> temp = Volatile.Read(ref NewMail);

        // 만약 하나라도 등록되어 있다면 모두 호출하여 이벤트가 발생했음을 통지한다.
        if (temp != null) temp(this, e);
    }
}
~~~

확장메서드를 이용하여 다중 스레드에 안정적인 로직을 캡슐화
~~~
public static class EventArgExtensions {
    public static void Raise<TEventArgs>(this TEventArgs e, Object sender, ref EventHandler<TEventArgs> eventDelegate) {
        EventHandler<TEventArgs> temp = Volatile.Read(ref eventDelegate);
        
        if (temp != null) temp(sender, e);
    }
}

protected virtual void OnNewMail(NewMailEventArgs e) {
    e.Raise(this, ref m_NewMail);
}
~~~

#### 4단계 입력 정보를 이용하여 올바르게 이벤트를 발생시키는 메서드 정의하기
~~~
internal class MailManager {
    public void SimulateNewMail(String from, String to, String subject) {
        NewMailEventArgs e = new NewMailEventArgs(from, to, subject);

        OnNewMail(e);
    }
}

### 이벤트가 기다리는 타입 설계하기
~~~
internal sealed class Fax {
    public Fax(MailManager mm) {
        // EventHander<NewMailEventArgs> 타입의 인스턴스를 만들어
        // FaxMsg 콜백 메서드를 MailManager의 NewMail 이벤트에 등록한다.
        mm.NewMail += FaxMsg;
    }

    private void FaxMsg(Object sender, NewMailEventArgs e) {
        // 'sender'는 MailManager 객체에 대한 참조를 가리키며 이를 활용할 수 있다.
        // 'e'는 MailManager가 이쪽으로 전달하기 원하는 이벤트에 대한 추가 정보를 포함하는 객체의 참조다.
        Console.WriteLine("메일을 팩스로 보내는 중:");
        Console.WriteLine(" 보낸사람={0}, 받는사람={1}, 제목={2}", e.From, e.To, e.Subject);
    }

    public void Unregister(MailManager mm) {
        // MailManager의 NewMail 이벤트에서 FaxMsg 메서드를 제거한다.
        mm.NewMail -= FaxMsg;
    }
}