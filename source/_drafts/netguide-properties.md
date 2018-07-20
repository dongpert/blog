---
title: netguide-properties
tags:
---
Properties are first class citizens in C#. The language defines syntax that enables developers to write code that accurately expresses their design intent.

속성은 C#에서 주요한 클래스 구성원 입니다. 언어는 개발자가 설계한 의도를 정확히 표현하는 코드를 작성할 수 있는 구문을 정의합니다.

Properties behave like fields when they are accessed. However, unlike fields, properties are implemented with accessors that define the statements executed when a property is accessed or assigned.

속성은 접근시 필드처럼 동작합니다. 그러나 필드와 같지 않고, 속성은 접근되거나 할당될때 실행되는 문장을 정의하는 접근자로 함께 구현됩니다.

## Property syntax
The syntax for properties is a natural extension to fields. A field defines a storage location:

속성에 대한 구문은 필드에 대한 자연적인 확장입니다. 필드는 저장 위치를 정의합니다.
~~~
public class Person
{
    public string FirstName;
}
~~~
A property definition contains declarations for a get and set accessor that retrieves and assigns the value of that property:

속성 정의는 속성의 값을 검색하고 할당하는 get 그리고 set 접근자에 대한 선언을 포함합니다.
~~~
public class Person
{
    public string FirstName { get; set; }
}
~~~
The syntax shown above is the auto property syntax. The compiler generates the storage location for the field that backs up the property. The compiler also implements the body of the get and set accessors.

위에 나타난 문법은 자동 속성 구문입니다. 컴파일러는 속성을 백업할 필드에 대한 저장위치를 생성합니다. 컴파일러는 get과 set 접근자의 몸체를 구현합니다.

Sometimes, you need to initialize a property to a value other than the default for its type. C# enables that by setting a value after the closing brace for the property. You may prefer the initial value for the FirstName property to be the empty string rather than null. You would specify that as shown below:

가끔 형식에 대한 기본과 다른 값으로 속성을 초기화할 필요가 있습니다. C#은 속성에 대한 중괄호를 닫은 후 값을 설정하여 가능하게 합니다. FirstName 속성에 대한 초기화 값을 null 보다 빈문자열로 택할 수 있습니다. 아래에서 보여주듯이 지정하면 됩니다.
~~~
public class Person
{
    public string FirstName { get; set; } = string.Empty;
}
~~~
Specific initialization is most useful for read-only properties, as you'll see later in this article.

초기화 지정은 읽기 전용 속성에 대부분 유용합니다.

You can also define the storage yourself, as shown below:

저장소를 직접 정의할 수 있습니다.
~~~
public class Person
{
    public string FirstName
    {
        get { return firstName; }
        set { firstName = value; }
    }
    private string firstName;
}
~~~
When a property implementation is a single expression, you can use expression-bodied members for the getter or setter:

속성 구현이 단일 식이면 getter와 setter에 대한 식 본문 멤버을 사용할 수 있습니다.
~~~
public class Person
{
    public string FirstName
    {
        get => firstName;
        set => firstName = value;
    }
    private string firstName;
}
~~~
This simplified syntax will be used where applicable throughout this article.

이 간소화한 구문은 해당하는 경우 기사 내내 사용됩니다.

The property definition shown above is a read-write property. Notice the keyword value in the set accessor. The set accessor always has a single parameter named value. The get accessor must return a value that is convertible to the type of the property (string in this example).

위에서 나타난 속성 정의는 읽기 전용 속성입니다. set 접근자 value 키워드에 주목하십시오. set 접근자는 항상 value라는 단일 매개변수를 가지고 있습니다. get 접근자는 속성의 형식으로 변환될수 있는 value를 반환해야 합니다.

That's the basics of the syntax. There are many different variations that support a variety of different design idioms. Let's explore, and learn the syntax options for each.

구문의 기본입니다. 다양한 디자인 관용구를 지원하는 다양한 변형이 있습니다. 각각에 대한 구문 옵션을 조사하고 배워보겠습니다.

## Scenarios
The examples above showed one of the simplest cases of property definition: a read-write property with no validation. By writing the code you want in the get and set accessors, you can create many different scenarios.

위의 예제는 속성 정의의 가장 단순한 경우 하나인 유효성 검사가 없는 읽기/쓰기 속성을 보여주었습니다. get과 set 접근자에서 원하는 코드를 작성하면 다양한 시나리오를 생성할 수 있습니다.

### Validation
You can write code in the set accessor to ensure that the values represented by a property are always valid. For example, suppose one rule for the Person class is that the name cannot be blank or white space. You would write that as follows:

속성이 표시한 값이 항상 유효한지 확인하기위해 접근자에서 코드를 작성할 수 있습니다. 예를 들어 Person 클래스에 대한 하나의 규칙이 이름이 비어있거나 화이트 스페이스일 수 없다고 가정합니다. 다음과 같이 작성해야 합니다.
~~~
public class Person
{
    public string FirstName
    {
        get => firstName;
        set
        {
            if (string.IsNullOrWhiteSpace(value))
                throw new ArgumentException("First name must not be blank");
            firstName = vaelu;
        }
    }
    private string firstName;
}
~~~
The preceding example can be simplified by using a throw expression as part of the property setter validation

이전 예제는 속성의 일부로 throw 식을 사용하여 setter 유효성 검사를 간소화될 수 있습니다.
~~~
public class Person
{
    public string FirstName
    {
        get => firstName;
        set => firstName = (!string.IsNullOrWhiteSpace(value)) ? value : throw new ArgumentException("First name must not be blank");
    }
    private string firstName;
}
~~~
The example above enforces the rule that the first name must not be blank or white space. If a developer writes

위 예제는 first name 빈 문자열 또는 화이트 스페이스가 되면 안된다는 규칙을 강제합니다.
~~~
hero.FirstName = "";
~~~
That assignment throws an ArgumentException. Because a property set accessor must have a void return type, you report errors in the set accessor by throwing an exception.

You can extend this same syntax to anything needed in your scenario. You can check the relationships between different properties, or validate against any external conditions. Any valid C# statements are valid in a property accessor.

시나리오에서 필요한 모든 곳에서 이와 동일한 구문을 확장할수 있습니다. 다양한 속성사이에 관계를 체크하거나 모든 외부 조건에 대하여 유효성 검사를 할 수 있습니다. 모든 유효한 C# 문장은 속성 접근자에서 유효합니다.

### Read-only
Up to this point, all the property definitions you have seen are read/write properties with public accessors. That's not the only valid accessibility for properties. You can create read-only properties, or give different accessibility to the set and get accessors. Suppose that your Person class should only enable changing the value of the FirstName property from other methods in that class. You could give the set accessor private accessibility instead of public:

이 시점까지 살펴본 모든 속성 정의는 public 접근자가 있는 읽기/쓰기 속성입니다. 이것이 속성에 대한 유일한 유효한 접근성이 아닙니다. 당신은 읽기전용 속성을 생성하거나 set 그리고 get접근자에 다양한 접근성을 줄 수 있습니다. Person 클래스가 클래스의 다른 메서드에서 FirstName 속성의 값을 변경 할 수만 있다고 가정하십시오. set 접근자에 public 대신 private 접근성을 주어야 됩니다.
~~~
public class Person
{
    public string FirstName { get; private set; }

    // remaining implementation removed from listing
}
~~~
Now, the FirstName property can be accessed from any code, but it can only be assigned from other code in the Person class.

지금 FirstName 속성 모든 코드에서 접근 될 수 있지만, Person 클래스에 다른 코드에서만 할당될 수 있습니다.

You can add any restrictive access modifier to either the set or get accessors. Any access modifier you place on the individual accessor must be more limited than the access modifier on the property definition. The above is legal because the FirstName property is public, but the set accessor is private. You could not declare a private property with a public accessor. Property declarations can also be declared protected, internal, protected internal, or, even private.

모든 제한적 접근 한정자를 set 또는 get 접근자에 추가할 수 있습니다. 개별 접근자에 배치하는 모든 접근 한정자는 속성 정의에 접근 한정자 보다 제한되어야 합니다. FirstName 속성은 public 이지만 set 접근자는 private이기 때문에 적법합니다.

It is also legal to place the more restrictive modifier on the get accessor. For example, you could have a public property, but restrict the get accessor to private. That scenario is rarely done in practice.

get 접근자에 더 제한적인 수정자를 위치시키는 것은 적합합니다. 예를 들어 public 속석을 가지고 있찌만 get 접근자를 private로 제한할 수 있습니다. 이 시나리오는 실제로 거의 수행되지 않습니다.

You can also restrict modifications to a property so that it can only be set in a constructor or a property initializer. You can modify the Person class so as follows:

생성자나 속성 초기화에서만 설정될 수 있게 하기 위해 속성에 제한적인 한정을 할 수 있습니다.
~~~
public clas Person
{
    public Person(string firstName) => this.FirstName = firstName;

    public string FirstName { get; }
}
~~~
This feature is most commonly used for initializing collections that are exposed as read-only properties:

이 특징은 읽기 전용 속성으로 노출된 컬렉션 초기화에 대해 대부분 공통적으로 사용됩니다.
~~~
public class Measurements
{
    public ICollection<DataPoint> points { get; } = new List<DataPoint>();
}
~~~
## Computed properties

A property does not need to simply return the value of a member field. You can create properties that return a computed value. Let's expand the Person object to return the full name, computed by concatenating the first and last names:

속성은 멤버의 필드의 값을 단순하게 반환하는 것을 필요로 하지 않습니다. 계산된 값을 반환하는 속성을 생성할 수 있습니다. Person 객체를 성과 이름을 연결하여 계산된 풀 네임을 반환하게 확장해보십시오.
~~~
public class Person
{
    public string FirstName { get; set; }
    public string FirstName { get; set; }
    public string FullName { get { return $"{FirstName {LastName}"; } }
}
~~~
The example above uses the string interpolation feature to create the formatted string for the full name.

위에 예제는 풀네임에 대해 형식화된 문자열을 생성하기 위해 문자열 보간 기능을 사용합니다.

You can also use an expression-bodied member, which provides a more succinct way to create the computed FullName property:

계산된 FullName 속성을 생성하기 위한 더 간결한 방법을 제공하는 식 본문 멤버 또한 사용할 수 있습니다.
~~~
public class Person
{
    public string FirstName { get; set; }

    public string LastName { get; set; }

    public string FullName => $"{FirstName} {LastName}";
}
~~~
Expression-bodied members use the lambda expression syntax to define methods that contain a single expression. Here, that expression returns the full name for the person object.

단일 식을 포함하는 메서드를 정의하기 위해 식 본문 멤버는 람다 식 구문을 사용합니다. 식은 사람 객체에 대한 풀 네임을 반환합니다.

## Cached evaluated properties
You can mix the concept of a computed property with storage and create a cached evaluated property. For example, you could update the FullName property so that the string formatting only happened the first time it was accessed:

계산된 속성의 개념을 저장소와 혼합하고 캐쉬된 평가 속성을 생성할 수 있습니다. 예를 들어 문자열 서식이 처음 접근 되었을 때만 발생하도록 FullName 속성을 업데이트 할 수 있습니다.
~~~
public class Person
{
    public string FirstName { get; set; }

    public string LastName { get; set; }

    public string firstName;
    public string FullName {
        get 
        {
            if (fullName == null)
                fullName = $"{FirstName} {LastName}";
            return fullName;
        }
    }
}
~~~
The above code contains a bug though. If code updates the value of either the FirstName or LastName property, the previously evaluated fullName field is invalid. You modify the set accessors of the FirstName and LastName property so that the fullName field is calculated again:

위 코드는 버그를 포함하고 있습니다. 코드가 FirstName 이나 LastName 속성의 값을 업데이트 한다면, 이전에 평가된 풀네임 필드는 유효하지 않습니다. fullName 필드가 다시 평가되게 FirstName 그리고 LastName 속성의 접근자의 set 접근자 수정합니다.
~~~
public class Person
{
    private string firstName;
    public string FirstName
    {
         get => fristName;
         set
         {
            firstName = value;
            fullName = null;
         }
    }

    private string lastName;
    public string LastName
    {
        get => lastName;
        set
        {
            lastName = value;
            fullName = null;
        }
    }

    public string firstName;
    public string FullName {
        get 
        {
            if (fullName == null)
                fullName = $"{FirstName} {LastName}";
            return fullName;
        }
    }
}
~~~
This final version evaluates the FullName property only when needed. If the previously calculated version is valid, it's used. If another state change invalidates the previously calculated version, it will be recalculated. Developers that use this class do not need to know the details of the implementation. None of these internal changes affect the use of the Person object. That's the key reason for using Properties to expose data members of an object.

마지막 버전은 필요할때만 FullName 속성을 평가합니다. 이전의 계산된 버전이 유효하다면 사용됩니다. 다른 상태 변화는 이전의 계산된 버전을 무효화합니다. 그것은 재평가 될 것입니다. 이 클래스를 사용하는 개발자는 구현의 세부사항을 알 필요가 없습니다. 이러한 내부 변경은 Person 객체의 사용에 영향을 주지 않습니다. 이것이 객체의 데이터 멤버를 노출하기 위해 속성을 사용하는 주요한 이유입니다.