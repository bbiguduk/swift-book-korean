# 자동 참조 카운팅 (Automatic Reference Counting)

객체의 생명주기와 관계를 모델링합니다.

Swift는 앱의 메모리 사용량을 추적하고 관리하기 위해
*자동 참조 카운팅(Automatic Reference Counting)*(ARC)을 사용합니다.
대부분의 경우에 Swift에서 메모리 관리는 "자동으로 동작"을 의미하고
메모리 관리에 대해 생각할 필요가 없습니다.
ARC는 인스턴스가 더 이상 필요치 않을 때
자동으로 해당 인스턴스가 사용하던 메모리를 해제합니다.

그러나 몇몇의 경우에 ARC는
메모리를 관리하기 위해
코드 간의 관계에 대한 추가 정보를 요구합니다.
여기에서는 이러한 상황을 설명하고
앱의 메모리를 관리하기 위해 ARC를 어떻게 사용하는지 보여줍니다.
Swift에서 ARC를 사용하는 것은
Objective-C에서 ARC 사용에 대한
[ARC 전환 릴리즈 노트 (Transitioning to ARC Release Notes)](https://developer.apple.com/library/content/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html)에서 설명한 접근 방식과 매우 유사합니다.

참조 카운팅은 클래스의 인스턴스에만 적용됩니다.
구조체와 열거형은 참조 타입이 아니고 값 타입이고
참조로 저장되거나 전달되지 않습니다.

## ARC의 동작 방식 (How ARC Works)

클래스의 새로운 인스턴스가 생성될 때마다
ARC는 인스턴스에 대한 정보를 저장하기 위해 메모리 공간을 할당합니다.
이 메모리는 인스턴스의 타입 정보와
해당 인스턴스와 연관된 저장 프로퍼티의 값이 포함됩니다.

또한 인스턴스가 더 이상 필요하지 않을 때,
ARC는 메모리가 다른 용도로 사용할 수 있도록
인스턴스가 사용한 메모리를 해제합니다.
이렇게 하면 클래스 인스턴스가 더 이상 필요하지 않을 때,
메모리를 차지하지 않도록 보장합니다.

그러나 ARC가 아직 사용 중인 인스턴스를 해제하면,
더 이상 해당 인스턴스의 프로퍼티에 접근하거나
해당 인스턴스의 메서드를 호출할 수 없습니다.
실제로 인스턴스에 접근하려고 하면 앱은 크래시가 발생합니다.

인스턴스가 여전히 필요한 동안 사라지지 않도록
ARC는 각 클래스 인스턴스를 참조하는
프로퍼티, 상수, 변수가 얼마나 많은지 추적합니다.
ARC는 인스턴스의 참조가 하나라도 존재하는 한
인스턴스를 해제하지 않습니다.

이것을 가능하게 하려면,
프로퍼티, 상수, 변수에 클래스 인스턴스를 할당할 때마다
해당 프로퍼티, 상수, 변수는 인스턴스에 *강한 참조(strong reference)*를 만듭니다.
이 참조를 "강한" 참조라고 하며
해당 인스턴스를 단단히 붙잡고 있어서
강한 참조가 남아있는 한 해제되지 않도록 합니다.

## ARC 동작 (ARC in Action)

다음은 어떻게 Automatic Reference Counting이 동작하는지 보여주는 예시입니다.
이 예시는 `Person` 이라는 간단한 클래스로 시작하고,
`name`이라는 저장 상수 프로퍼티를 정의합니다:

```swift
class Person {
    let name: String
    init(name: String) {
        self.name = name
        print("\(name) is being initialized")
    }
    deinit {
        print("\(name) is being deinitialized")
    }
}
```

<!--
  - test: `howARCWorks`

  ```swifttest
  -> class Person {
        let name: String
        init(name: String) {
           self.name = name
           print("\(name) is being initialized")
        }
        deinit {
           print("\(name) is being deinitialized")
        }
     }
  ```
-->

`Person` 클래스는 인스턴스의 `name` 프로퍼티를 설정하고
메세지를 출력하는 이니셜라이저를 가지고 있습니다.
`Person` 클래스는 클래스의 인스턴스가 해제될 때 메시지를 출력하는
디이니셜라이저도 가지고 있습니다.

다음 코드에서는 `Person?` 타입의 세 개의 변수를 정의하고,
이것은 다음 코드에서
새로운 `Person` 인스턴스에 여러 참조를 설정하기 위해 사용됩니다.
이 변수는 `Person`이 아닌 `Person?`인 옵셔널 타입 이므로,
`nil`로 자동으로 초기화되고
현재는 `Person` 인스턴스를 참조하지 않습니다.

```swift
var reference1: Person?
var reference2: Person?
var reference3: Person?
```

<!--
  - test: `howARCWorks`

  ```swifttest
  -> var reference1: Person?
  -> var reference2: Person?
  -> var reference3: Person?
  ```
-->

이제 새로운 `Person` 인스턴스를 생성하고
이 세 개의 변수 중 하나에 할당합니다:

```swift
reference1 = Person(name: "John Appleseed")
// Prints "John Appleseed is being initialized"
```

<!--
  - test: `howARCWorks`

  ```swifttest
  -> reference1 = Person(name: "John Appleseed")
  <- John Appleseed is being initialized
  ```
-->

`Person` 클래스의 이니셜라이저를 호출하면
`"John Appleseed is being initialized"` 메세지가 출력됩니다.
이것은 초기화가 발생했음을 의미합니다.

새로운 `Person` 인스턴스는 `reference1` 변수에 할당되기 때문에
이제 `reference1`에서 새로운 `Person` 인스턴스에 대한 강한 참조가 생깁니다.
하나의 강한 참조가 있기 때문에
ARC는 이 `Person`을 메모리에 유지하고 해제하지 않습니다.

동일한 `Person` 인스턴스를 두 변수에 할당하면,
해당 인스턴스에 대한 강한 참조가 두 개 더 설정됩니다:

```swift
reference2 = reference1
reference3 = reference1
```

<!--
  - test: `howARCWorks`

  ```swifttest
  -> reference2 = reference1
  -> reference3 = reference1
  ```
-->

이제 이 단일 `Person` 인스턴스에 *세 개*의 강한 참조가 있습니다.

기존의 참조를 포함하여 두 개의 변수에 `nil`을 할당하여
강한 참조를 중단하면
하나의 강한 참조만 남고
`Person` 인스턴스는 해제되지 않습니다:

```swift
reference1 = nil
reference2 = nil
```

<!--
  - test: `howARCWorks`

  ```swifttest
  -> reference1 = nil
  -> reference2 = nil
  ```
-->

ARC는 마지막 강한 참조를 중단할 때까지
`Person` 인스턴스를 해제하지 않고,
마지막 강한 참조가 중단되면 `Person` 인스턴스를 더 이상 사용하지 않는 것이 명백해집니다:

```swift
reference3 = nil
// Prints "John Appleseed is being deinitialized"
```

<!--
  - test: `howARCWorks`

  ```swifttest
  -> reference3 = nil
  <- John Appleseed is being deinitialized
  ```
-->

## 클래스 인스턴스 간의 강한 순환 참조 (Strong Reference Cycles Between Class Instances)

위의 예시에서
ARC는 새로 생성한 `Person` 인스턴스의 참조 개수를 추적하고,
더 이상 필요하지 않을 때 `Person` 인스턴스를 해제할 수 있습니다.

그러나 클래스 인스턴스가 *절대* 강한 참조가 0이 될 수 없는
코드를 작성할 수 있습니다.
이는 두 클래스 인스턴스가 서로에 대한 강한 참조를 유지하면
각 인스턴스가 서로를 계속 살아 있게 만듭니다.
이것을 *강한 순환 참조(strong reference cycle)*라고 합니다.

강한 순환 참조를 해결하려면
클래스 간의 관계를
강한 참조 대신 약한 참조(weak)나 미소유 참조(unowned)로 정의해야 합니다.
이 프로세스는
<doc:AutomaticReferenceCounting#클래스-인스턴스-간의-강한-순환-참조-해결-Resolving-Strong-Reference-Cycles-Between-Class-Instances>에 설명되어 있습니다.
그러나 강한 순환 참조을 어떻게 해결하는지 배우기 전에
어떻게 순환이 발생하는지 이해하는 것이 좋습니다.

다음은 실수로 강한 순환 참조을 생성하는 예시입니다.
이 예시는 `Person`과 `Apartment`라는 두 개의 클래스를 정의하고,
아파트와 거주자를 모델링합니다:

```swift
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
    deinit { print("\(name) is being deinitialized") }
}

class Apartment {
    let unit: String
    init(unit: String) { self.unit = unit }
    var tenant: Person?
    deinit { print("Apartment \(unit) is being deinitialized") }
}
```

<!--
  - test: `referenceCycles`

  ```swifttest
  -> class Person {
        let name: String
        init(name: String) { self.name = name }
        var apartment: Apartment?
        deinit { print("\(name) is being deinitialized") }
     }
  ---
  -> class Apartment {
        let unit: String
        init(unit: String) { self.unit = unit }
        var tenant: Person?
        deinit { print("Apartment \(unit) is being deinitialized") }
     }
  ```
-->

모든 `Person` 인스턴스는 `String` 타입의 `name` 프로퍼티와
초기 값이 `nil`인 옵셔널 `apartment` 프로퍼티를 가지고 있습니다.
`apartment` 프로퍼티는 사람이 항상 아파트를 가지고 있지 않기 때문에 옵셔널입니다.

유사하게 모든 `Apartment` 인스턴스는 `String` 타입의 `unit` 프로퍼티와
초기 값이 `nil`인 옵셔널 `tenant` 프로퍼티를 가지고 있습니다.
`tenant` 프로퍼티는 아파트가 항상 세입자를 보유하는 것이 아니므로 옵셔널입니다.

이 클래스 모두 디이니셜라이저를 정의하고,
클래스의 인스턴스가 해제될 때 메세지를 출력합니다.
이것을 통해 `Person`과 `Apartment`의 인스턴스가 해제되는지
확인할 수 있습니다.

다음 코드는 `Apartment`와 `Person` 인스턴스를 설정할
`john`과 `unit4A`라는
옵셔널 타입의 두 개의 변수를 정의합니다.
이 변수 모두 옵셔널이므로 초기 값으로 `nil`을 가집니다:

```swift
var john: Person?
var unit4A: Apartment?
```

<!--
  - test: `referenceCycles`

  ```swifttest
  -> var john: Person?
  -> var unit4A: Apartment?
  ```
-->

이제 `Person` 인스턴스와 `Apartment` 인스턴스를 생성할 수 있고,
이 인스턴스를 `john`과 `unit4A` 변수에 할당할 수 있습니다:

```swift
john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")
```

<!--
  - test: `referenceCycles`

  ```swifttest
  -> john = Person(name: "John Appleseed")
  -> unit4A = Apartment(unit: "4A")
  ```
-->

다음은 강한 참조가 이 두 인스턴스를 생성하고 할당한 후에 어떻게 되는지 보여줍니다.
`john` 변수는 이제 `Person` 인스턴스에 대해 강한 참조를 가지고 있고,
`unit4A` 변수는 `Apartment` 인스턴스에 대한 강한 참조를 가지고 있습니다:

![Reference Cycle 01](referenceCycle01)

이제 사람은 아파트를 가지고 아파트는 소유자를 가지도록
두 인스턴스를 함께 연결할 수 있습니다.
느낌표(`!`)는 `john`과 `unit4A` 옵셔널 변수에 저장된 인스턴스를
언래핑하고 접근하기 위해 사용되므로
해당 인스턴스의 프로퍼티를 설정할 수 있습니다:

```swift
john!.apartment = unit4A
unit4A!.tenant = john
```

<!--
  - test: `referenceCycles`

  ```swifttest
  -> john!.apartment = unit4A
  -> unit4A!.tenant = john
  ```
-->

다음은 두 인스턴스를 함께 연결한 후에 강한 참조가 어떻게 되는지 보여줍니다:

![Reference Cycle 02](referenceCycle02)

불행히도 이 두 인스턴스 연결은
서로 간의 강한 순환 참조를 생성합니다.
`Person` 인스턴스는 이제 `Apartment` 인스턴스에 대한 강한 참조를 가지고,
`Apartment` 인스턴스는 `Person` 인스턴스에 대한 강한 참조를 가집니다.
따라서 `john`과 `unit4A` 변수의
강한 참조를 중단해도
참조 카운트는 0으로 떨어지지 않고
인스턴스는 ARC에 의해 해제되지 않습니다:

```swift
john = nil
unit4A = nil
```

<!--
  - test: `referenceCycles`

  ```swifttest
  -> john = nil
  -> unit4A = nil
  ```
-->

두 변수를 `nil`로 설정해도
디이니셜라이저는 호출되지 않습니다.
강한 순환 참조는 `Person`과 `Apartment` 인스턴스가
해제되는 것을 방지하여 앱에서 메모리 누수를 유발합니다.

다음은 `john`과 `unit4A` 변수를 `nil`로
설정한 후에 강한 참조를 나타냅니다:

![Reference Cycle 03](referenceCycle03)

`Person`과 `Apartment` 인스턴스 간의
강한 참조는 남아있고 중단할 수 없습니다.

## 클래스 인스턴스 간의 강한 순환 참조 해결 (Resolving Strong Reference Cycles Between Class Instances)

Swift는 클래스 타입의 프로퍼티에서
강한 순환 참조를 해결하기 위해 두 가지 방법을 제공합니다:
약한 참조(weak references)와 미소유 참조(unowned references)입니다.

약한 참조와 미소유 참조를 사용하면
순환 참조의 한 인스턴스가 다른 인스턴스를 강하게 참조하지 *않고* 참조할 수 있습니다.
이렇게 하면 인스턴스는 강한 순환 참조를 만들지 않고도 서로를 참조할 수 있습니다.

다른 인스턴스의 생명주기가 더 짧은 경우 ---
즉, 다른 인스턴스가 먼저 해제될 수 있을 때 약한 참조를 사용합니다.
위의 `Apartment` 예시에서
아파트는 어느 시점에
세입자가 없을 수 있으므로
이러한 경우 약한 참조는 참조 사이클을 끊는 적절한 방법입니다.
반대로 다른 인스턴스의 생명주기가 동일하거나
더 긴 경우 미소유 참조를 사용합니다.

<!--
  QUESTION: how do I answer the question
  "which of the two properties in the reference cycle
  should be marked as weak or unowned?"
-->

### 약한 참조 (Weak References)

*약한 참조(weak reference)*는 참조하는 인스턴스를
강하게 유지하지 않는 참조이므로
ARC가 참조된 인스턴스를 해제하는 것을 중지하지 않습니다.
이러한 동작은 참조가 강한 순환 참조가 되는 것을 방지합니다.
약한 참조는 프로퍼티나 변수 선언 전에
`weak` 키워드를 위치시켜 나타냅니다.

약한 참조는 참조하는 인스턴스를 강하게 유지하지 않기 때문에
약한 참조가 참조하는 동안
해당 인스턴스가 해제될 수 있습니다.
따라서 ARC는 참조하는 인스턴스가 해제되면
`nil`로 약한 참조를 자동으로 설정합니다.
그리고 약한 참조는
런타임에 값을 `nil`로 변경하는 것을 허락해야 하므로
항상 옵셔널 타입의 상수가 아닌 변수로 선언됩니다.

다른 옵셔널 값과 같이
약한 참조의 값 존재를 확인할 수 있고
더 이상 존재하지 않는 유효하지 않은
인스턴스에 대해 참조하는 일은 없습니다.

> Note: 프로퍼티 관찰자는
> ARC가 약한 참조를 `nil`로 설정할 때 호출되지 않습니다.

<!--
  - test: `weak-reference-doesnt-trigger-didset`

  ```swifttest
  -> class C {
         weak var w: C? { didSet { print("did set") } }
     }
  -> var c1 = C()
  -> do {
  ->     let c2 = C()
  ->     assert(c1.w == nil)
  ->     c1.w = c2
  << did set
  ->     assert(c1.w != nil)
  -> } // ARC deallocates c2; didSet doesn't fire.
  -> assert(c1.w == nil)
  ```
-->

아래의 예시는 위의 `Person`과 `Apartment` 예시와 동일하지만,
한 가지 중요한 차이점이 있습니다.
이번에는 `Apartment` 타입의 `tenant` 프로퍼티가
약한 참조로 선언됩니다:

```swift
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
    deinit { print("\(name) is being deinitialized") }
}

class Apartment {
    let unit: String
    init(unit: String) { self.unit = unit }
    weak var tenant: Person?
    deinit { print("Apartment \(unit) is being deinitialized") }
}
```

<!--
  - test: `weakReferences`

  ```swifttest
  -> class Person {
        let name: String
        init(name: String) { self.name = name }
        var apartment: Apartment?
        deinit { print("\(name) is being deinitialized") }
     }
  ---
  -> class Apartment {
        let unit: String
        init(unit: String) { self.unit = unit }
        weak var tenant: Person?
        deinit { print("Apartment \(unit) is being deinitialized") }
     }
  ```
-->

두 변수(`john`과 `unit4A`)에서의 강한 참조와
두 인스턴스 간의 연결은 이전과 같이 생성됩니다:

```swift
var john: Person?
var unit4A: Apartment?

john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")

john!.apartment = unit4A
unit4A!.tenant = john
```

<!--
  - test: `weakReferences`

  ```swifttest
  -> var john: Person?
  -> var unit4A: Apartment?
  ---
  -> john = Person(name: "John Appleseed")
  -> unit4A = Apartment(unit: "4A")
  ---
  -> john!.apartment = unit4A
  -> unit4A!.tenant = john
  ```
-->

다음은 두 인스턴스를 연결되면 어떻게 참조되는지 보여줍니다:

![Weak Reference 01](weakReference01)

`Person` 인스턴스는 `Apartment` 인스턴스에 대해 여전히 강한 참조를 가지고 있지만
`Apartment` 인스턴스는 이제 `Person` 인스턴스에 대해 *약한* 참조를 가지고 있습니다.
이것은 `john` 변수에 `nil`을 설정하여
강한 참조를 끊으면
`Person` 인스턴스에 대해 더 이상 강한 참조가 아님을 의미합니다:

```swift
john = nil
// Prints "John Appleseed is being deinitialized"
```

<!--
  - test: `weakReferences`

  ```swifttest
  -> john = nil
  <- John Appleseed is being deinitialized
  ```
-->

더 이상 `Person` 인스턴스에 대해 강한 참조를 가지지 않기 때문에,
해제되고
`tenant` 프로퍼티는 `nil`로 설정됩니다:

![Weak Reference 02](weakReference02)

`Apartment` 인스턴스에 대한 유일한 강한 참조는
`unit4A` 변수에서 가져온 것입니다.
*해당* 강한 참조를 끊으면,
`Apartment` 인스턴스에 대한 강한 참조는 더 이상 없습니다:

```swift
unit4A = nil
// Prints "Apartment 4A is being deinitialized"
```

<!--
  - test: `weakReferences`

  ```swifttest
  -> unit4A = nil
  <- Apartment 4A is being deinitialized
  ```
-->

 더 이상 `Apartment` 인스턴스에 대한 강한 참조를 가지지 않기 때문에,
 이것 또한 해제됩니다:

![Weak Reference 03](weakReference03)

> Note: 가비지 컬렉션을 사용하는 시스템에서는
> 메모리 압력이 가비지 컬렉션을 트리거 할 때만
> 강한 참조가 없는 객체가 할당 해제되기 때문에
> 간단한 캐싱 메커니즘을 구현하는데 약한 포인터가 사용되는 경우가 있습니다.
> 그러나 ARC를 사용하면
> 마지막 강한 참조가 제거되자마자
> 값이 해제되어 약한 참조는 이러한 용도에 적합하지 않습니다.

### 미소유 참조 (Unowned References)

약한 참조와 마찬가지로
*미소유 참조(unowned reference)*도
참조하는 인스턴스를 강하게 유지하지 않습니다.
그러나 약한 참조와 다르게
미소유 참조는 다른 인스턴스의
생명주기가 같거나 더 긴 경우에 사용합니다.
미소유 참조는 프로퍼티나 변수 선언 전에
`unowned` 키워드를 위치시켜 나타냅니다.

약한 참조와 달리
미소유 참조는 항상 값을 가지고 있는 것으로 예상합니다.
결과적으로
미소유로 만들어진 값은 옵셔널이 아니며,
ARC는 미소유 참조의 값을 `nil`로 설정하지 않습니다.

<!--
  Everything that unowned can do, weak can do slower and more awkwardly
  (but still correctly).
  Unowned is interesting because it's faster and easier (no optionals) ---
  in the cases where it's actually correct for your data.
-->

> Important: 미소유 참조는 참조 대상 인스턴스가 해제되지 않았다는 것을
> *항상* 확신하는 경우에만 사용합니다.
>
> 인스턴스가 해제된 후에
> 미소유 참조의 값에 접근하려고 하면
> 런타임 오류가 발생합니다.

<!--
  One way to satisfy that requirement is to
  always access objects that have unmanaged properties through their owner
  instead of keeping a reference to them directly,
  because those direct references could outlive the owner.
  However... this strategy really only works when the unowned reference
  is a backpointer from an object up to its owner.
-->

다음 예시는 은행 고객과 고객의 신용카드를 모델링하는
`Customer`와 `CreditCard`인 두 개의 클래스를 정의합니다.
이 두 클래스는 프로퍼티로 서로의 인스턴스를 각각 저장하고,
강한 순환 참조가 발생할 가능성이 있습니다.

`Customer`와 `CreditCard` 간의 관계는
위의 약한 참조 예시에서 본
`Apartment`와 `Person` 간의 관계와 약간 다릅니다.
이 데이터 모델에서 고객은 신용카드를 가지고 있거나 가지고 있지 않을 수 있지만
신용카드는 *항상* 고객과 연관되어 있습니다.
`CreditCard` 인스턴스는 참조하는 `Customer`보다 오래 지속되지 않습니다.
이것을 표현하기 위해 `Customer` 클래스는 옵셔널 `card` 프로퍼티를 가지지만
`CreditCard` 클래스는 미소유(옵셔널 아님) `customer` 프로퍼티를 가집니다.

또한 새로운 `CreditCard` 인스턴스는
커스텀 `CreditCard` 이니셜라이저에
`number` 값과 `customer` 인스턴스를 *전달해야만* 생성할 수 있습니다.
이렇게 하면 `CreditCard` 인스턴스가 생성될 때
`CreditCard` 인스턴스에 항상 연관된 `customer` 인스턴스를 가지고 있습니다.

신용카드는 항상 고객이 있으므로
강한 순환 참조를 피하기 위해
`customer` 프로퍼티에 미소유 참조로 정의합니다:

```swift
class Customer {
    let name: String
    var card: CreditCard?
    init(name: String) {
        self.name = name
    }
    deinit { print("\(name) is being deinitialized") }
}

class CreditCard {
    let number: UInt64
    unowned let customer: Customer
    init(number: UInt64, customer: Customer) {
        self.number = number
        self.customer = customer
    }
    deinit { print("Card #\(number) is being deinitialized") }
}
```

<!--
  - test: `unownedReferences`

  ```swifttest
  -> class Customer {
        let name: String
        var card: CreditCard?
        init(name: String) {
           self.name = name
        }
        deinit { print("\(name) is being deinitialized") }
     }
  ---
  -> class CreditCard {
        let number: UInt64
        unowned let customer: Customer
        init(number: UInt64, customer: Customer) {
           self.number = number
           self.customer = customer
        }
        deinit { print("Card #\(number) is being deinitialized") }
     }
  ```
-->

> Note: `CreditCard` 클래스의 `number` 프로퍼티는
> `Int`가 아닌 `UInt64` 타입으로 정의되어
> `number` 프로퍼티의 용량이
> 32비트와 64비트 시스템 모두에 16자리 카드번호를 저장할 수 있을만큼 충분히 크도록 합니다.

다음 코드는 `john`이라는 옵셔널 `Customer` 변수를 정의하고,
구체적인 고객에 대한 참조를 저장하는데 사용합니다.
이 변수는 옵셔널이므로 `nil`의 초기 값을 가집니다:

```swift
var john: Customer?
```

<!--
  - test: `unownedReferences`

  ```swifttest
  -> var john: Customer?
  ```
-->

이제 `Customer` 인스턴스를 생성할 수 있고
해당 고객의 `card` 프로퍼티로
새로운 `CreditCard` 인스턴스를 초기화하고 할당할 수 있습니다:

```swift
john = Customer(name: "John Appleseed")
john!.card = CreditCard(number: 1234_5678_9012_3456, customer: john!)
```

<!--
  - test: `unownedReferences`

  ```swifttest
  -> john = Customer(name: "John Appleseed")
  -> john!.card = CreditCard(number: 1234_5678_9012_3456, customer: john!)
  ```
-->

다음은 두 인스턴스가 연결되어 어떻게 참조하는지 보여줍니다:

![Unowned Reference 01](unownedReference01)

`Customer` 인스턴스는 이제 `CreditCard` 인스턴스에 대한 강한 참조를 가지고 있고
`CreditCard` 인스턴스는 `Customer` 인스턴스에 대해 미소유 참조를 가지고 있습니다.

미소유 `customer` 참조 때문에,
`john` 변수의 강한 참조를 끊으면
`Customer` 인스턴스에 대해 더 이상 강한 참조를 가지지 않습니다:

![Unowned Reference 02](unownedReference02)

더 이상 `Customer` 인스턴스에 대해 강한 참조가 아니므로
해제됩니다.
그 다음에
더 이상 `CreditCard` 인스턴스에 대해 강한 참조가 아니므로
이것도 해제됩니다:

```swift
john = nil
// Prints "John Appleseed is being deinitialized"
// Prints "Card #1234567890123456 is being deinitialized"
```

<!--
  - test: `unownedReferences`

  ```swifttest
  -> john = nil
  <- John Appleseed is being deinitialized
  <- Card #1234567890123456 is being deinitialized
  ```
-->

위의 마지막 코드는
`john` 변수를 `nil`로 설정한 후에
`Customer` 인스턴스와 `CreditCard` 인스턴스 모두
"deinitialized"를 출력하는 디이니셜라이저를 보여줍니다.

> Note: 위의 예시는 *안전한* 미소유 참조를 어떻게 사용하는지 보여줍니다.
> Swift는 런타임 안전 검사가 필요하지 않을 때
> 사용하는 *안전하지 않은* 미소유 참조(unsafe unowned references)를 제공합니다 ---
> 예를 들어 성능상의 이유가 한 예입니다.
> 모든 안전하지 않은 작업과 마찬가지로
> 해당 코드의 안정성은 개발자가 직접 책임져야 합니다.
>
> 안전하지 않은 미소유 참조는 `unowned(unsafe)`로 표시합니다.
> 참조하는 인스턴스가 해제된 후에
> 안전하지 않은 미소유 참조에 접근하려고 하면
> 프로그램은 인스턴스가 사용한
> 메모리 위치에 접근하고,
> 이것은 안전하지 않은 작업입니다.

<!--
  <rdar://problem/28805121> TSPL: ARC - Add discussion of "unowned" with different lifetimes
  Try expanding the example above so each customer has an array of credit cards.
-->

### 미소유 옵셔널 참조 (Unowned Optional References)

클래스에 대한 옵셔널 참조도 미소유로 표기할 수 있습니다.
ARC 소유권 모델 측면에서
미소유 옵셔널 참조(unowned optional reference)와 약한 참조는
모두 같은 컨텍스트에서 사용할 수 있습니다.
차이점은 미소유 옵셔널 참조를 사용할 때,
유효한 객체를 참조하거나
`nil`로 설정해야 합니다.

다음은 학교의 학과에서 제공하는
강좌를 모델링하는 예시입니다:

```swift
class Department {
    var name: String
    var courses: [Course]
    init(name: String) {
        self.name = name
        self.courses = []
    }
}

class Course {
    var name: String
    unowned var department: Department
    unowned var nextCourse: Course?
    init(name: String, in department: Department) {
        self.name = name
        self.department = department
        self.nextCourse = nil
    }
}
```

<!--
  - test: `unowned-optional-references`

  ```swifttest
  -> class Department {
         var name: String
         var courses: [Course]
         init(name: String) {
             self.name = name
             self.courses = []
         }
     }
  ---
  -> class Course {
         var name: String
         unowned var department: Department
         unowned var nextCourse: Course?
         init(name: String, in department: Department) {
             self.name = name
             self.department = department
             self.nextCourse = nil
         }
     }
  ```
-->

`Department`는 학과에서 제공하는 각 강좌에
강한 참조를 유지합니다.
ARC 소유권 모델에서 학과는 강좌를 소유하고 있습니다.
`Course`는 두 개의 미소유 참조를 가지고 있으며,
하나는 학과에 대한 것이고,
다른 하나는 학생이 수강해야 될 강좌입니다;
`Course`는 이 두 객체를 소유하지 않습니다.
모든 강좌는 학과에 속하므로
`department` 프로퍼티는 옵셔널이 아닙니다.
그러나
일부 강좌는 후속 과정이 없기 때문에
`nextCourse` 프로퍼티는 옵셔널 입니다.

다음은 이러한 클래스를 사용하는 예시입니다:

```swift
let department = Department(name: "Horticulture")

let intro = Course(name: "Survey of Plants", in: department)
let intermediate = Course(name: "Growing Common Herbs", in: department)
let advanced = Course(name: "Caring for Tropical Plants", in: department)

intro.nextCourse = intermediate
intermediate.nextCourse = advanced
department.courses = [intro, intermediate, advanced]
```

<!--
  - test: `unowned-optional-references`

  ```swifttest
  -> let department = Department(name: "Horticulture")
  ---
  -> let intro = Course(name: "Survey of Plants", in: department)
  -> let intermediate = Course(name: "Growing Common Herbs", in: department)
  -> let advanced = Course(name: "Caring for Tropical Plants", in: department)
  ---
  -> intro.nextCourse = intermediate
  -> intermediate.nextCourse = advanced
  -> department.courses = [intro, intermediate, advanced]
  ```
-->

위의 코드는 학과와 그 학과의 세 개의 강좌를 생성합니다.
입문과 중급 과목 모두 `nextCourse` 프로퍼티에 저장된
다음 과목을 제안하며
이는 학생이 과목을 완료한 후 수강해야 하는 과목에 대한
미소유 옵셔널 참조를 유지합니다.

![Unowned Optional Reference](unownedOptionalReference)

미소유 옵셔널 참조는
참조하는 클래스 인스턴스를 강하게 유지하지 않으므로,
ARC가 해당 인스턴스를 해제하는 것을 제한하지 않습니다.
ARC에서 미소유 참조와 동일하게 동작하지만,
미소유 옵셔널 참조는 `nil`이 될 수 있다는 점이 다릅니다.

미소유 참조와 마찬가지로
`nextCourse`가 항상 해제되지 않은 강좌를 참조하도록
직접 관리해야 합니다.
예를 들어
`department.courses`에서 강좌를 삭제할 때
다른 강좌가 해당 강좌를 참조하고 있다면
그 참조도 삭제해야 합니다.

> Note: 옵셔널 값의 타입은 `Optional`이며,
> Swift 표준 라이브러리의 열거형입니다.
> 그러나 옵셔널은 값 타입에 `unowned`를 표시할 수 없는
> 규칙에 대해 예외입니다.
>
> 클래스를 감싸는 옵셔널은
> 참조 카운팅을 사용하지 않으므로,
> 옵셔널에 대해 강한 참조를 유지할 필요가 없습니다.

<!--
  - test: `unowned-can-be-optional`

  ```swifttest
  >> class C { var x = 100 }
  >> class D {
  >>     unowned var a: C
  >>     unowned var b: C?
  >>     init(value: C) {
  >>         self.a = value
  >>         self.b = value
  >>     }
  >> }
  >> var c = C() as C?
  >> let d = D(value: c! )
  >> print(d.a.x, d.b?.x as Any)
  << 100 Optional(100)
  ---
  >> c = nil
  // Now that the C instance is deallocated, access to d.a is an error.
  // We manually nil out d.b, which is safe because d.b is an Optional and the
  // enum stays in memory regardless of ARC deallocating the C instance.
  >> d.b = nil
  >> print(d.b?.x as Any)
  << nil
  ```
-->

### 미소유 참조와 암묵적 언래핑된 옵셔널 프로퍼티 \(Unowned References and Implicitly Unwrapped Optional Properties\)

위에서 약한 참조와 미소유 참조에 대한 예시는 강한 순환 참조을 중단해야 하는 일반적인 2가지 시나리오를 다룹니다.

`Person` 과 `Apartment` 예시는 둘 다 `nil` 이 될 수 있는 프로퍼티가 강한 순환 참조을 유발할 수 있는 가능성이 있는 상황을 보여줍니다. 이 시나리오는 약한 참조로 해결하는 것이 가장 좋습니다.

`Customer` 와 `CreditCard` 예시는 `nil` 이 허용되는 하나의 프로퍼티와 `nil` 일 수 없는 프로퍼티가 강한 순환 참조을 유발할 수 있는 가능성이 있는 상황을 보여줍니다. 이 시나리오는 미소유 참조로 해결하는 것이 가장 좋습니다.

그러나 두 프로퍼티 모두 항상 값이 있고 초기화가 완료되면 `nil` 이 되어서는 안되는 세번째 시나리오가 있습니다. 이 시나리오에서는 한 클래스의 미소유 프로퍼티를 다른 클래스에 암시적으로 언래핑된 옵셔널 프로퍼티와 결합하는 것이 유용합니다.

이렇게 하면 참조 사이클을 피하면서 초기화가 완료되면 두 프로퍼티 모두에 옵셔널 언래핑 없이 직접적으로 접근할 수 있습니다. 이 섹션에서는 이러한 관계를 어떻게 설정하는지 보여줍니다.

아래의 예시는 프로퍼티로 다른 클래스의 인스턴스를 각 저장하는 `Country` 와 `City` 인 2개의 클래스를 정의합니다. 이 데이터 모델에서 모든 국가는 항상 수도를 가지고 있고 모든 도시는 항상 국가에 속해 있어야 합니다. 이것을 표현하기 위해 `Country` 클래스는 `capitalCity` 프로퍼티를 가지고 있고 `City` 클래스는 `country` 프로퍼티를 가지고 있습니다:

```swift
class Country {
    let name: String
    var capitalCity: City!
    init(name: String, capitalName: String) {
        self.name = name
        self.capitalCity = City(name: capitalName, country: self)
    }
}

class City {
    let name: String
    unowned let country: Country
    init(name: String, country: Country) {
        self.name = name
        self.country = country
    }
}
```

두 클래스 간의 상호 종속성을 설정하기 위해 `City` 에 대한 이니셜라이저는 `Country` 인스턴스를 가지고 있고 `country` 프로퍼티에 저장합니다.

`City` 에 대한 이니셜라이저는 `Country` 에 대한 이니셜라이저 내에서 호출됩니다. 그러나 `Country` 에 대한 이니셜라이저는 <doc:Initialization#2단계-초기화-Two-Phase-Initialization> 에서 설명했듯이 새로운 `Country` 인스턴스가 완벽히 초기화 될 때까지 `City` 이니셜라이저에 `self` 를 전달할 수 없습니다.

이 요구사항을 처리하려면 `Country` 의 `capitalCity` 프로퍼티를 타입 설명의 끝에 느낌표 \(`City!`\)로 표시되는 암시적 언래핑된 옵셔널 프로퍼티로 선언합니다. `capitalCity` 프로퍼티는 다른 옵셔널과 같이 `nil` 의 기본값을 가지지만 <doc:TheBasics#암시적으로-언래핑된-옵셔널-Implicitly-Unwrapped-Optionals> 에서 설명했듯이 언래핑 할 필요없이 값에 접근할 수 있다는 의미입니다.

`capitalCity` 는 기본 `nil` 값을 가지므로 새로운 `Country` 인스턴스는 `Country` 인스턴스가 이니셜라이저 내에서 `name` 프로퍼티를 설정하는 즉시 새로운 `Country`인스턴스는 완벽히 초기화 된 것으로 간주합니다. 이것은 `Country` 이니셜라이저는 `name` 프로퍼티가 설정되는 즉시 암시적 `self` 프로퍼티를 참조하고 전달할 수 있다는 의미입니다. 따라서 `Country` 이니셜라이저는 `capitalCity` 프로퍼티를 설정할 때 `City` 이니셜라이저에 대한 하나의 파라미터로 `self` 를 전달할 수 있습니다.

이 모든 것은 강한 순환 참조을 만들지 않고 단일 구문으로 `Country` 와 `City` 인스턴스를 생성하고 `capitalCity` 프로퍼티는 옵셔널 값을 언래핑 하기위해 느낌표를 사용할 필요없이 직접 접근할 수 있다는 의미입니다:

```swift
var country = Country(name: "Canada", capitalName: "Ottawa")
print("\(country.name)'s capital city is called \(country.capitalCity.name)")
// Prints "Canada's capital city is called Ottawa"
```

위의 예시에서 암시적 언래핑된 옵셔널의 사용은 모든 2단계 클래스 이니셜라이저 요구사항을 충족한다는 의미입니다. `capitalCity` 프로퍼티는 초기화가 완료되면 옵셔널이 아닌 값처럼 사용되고 접근할 수 있지만 강한 순환 참조은 피할 수 있습니다.

## 클로저에 대한 강한 순환 참조 \(Strong Reference Cycles for Closures\)

위에서 두 클래스 인스턴스 프로퍼티가 서로 강한 참조를 유지할 때 강한 순환 참조이 어떻게 생성될 수 있는지 보았습니다. 또한 강한 순환 참조을 끊기 위해 약한 참조와 미소유 참조를 어떻게 사용해야 하는지 보았습니다.

강한 순환 참조은 클래스 인스턴스의 프로퍼티에 클로저를 할당하고 해당 클로저의 본문에 인스턴스를 캡처하는 경우에도 발생할 수 있습니다. 이 캡처는 클로저의 본문은 `self.someProperty` 와 같이 인스턴스의 프로퍼티에 접근하거나 클로저는 `self.someMethod()` 와 같이 인스턴스의 메서드를 호출하기 때문에 발생할 수 있습니다. 두 경우 모두 이러한 접근은 클로저가 `self` 를 "캡처" 하여 강한 순환 참조을 생성합니다.

이 강한 순환 참조은 클래스와 같이 클로저는 _참조 타입 \(reference types\)_ 이기 때문에 발생합니다. 프로퍼티에 클로저를 할당하면 해당 클로저에 _참조_ 를 할당하는 것입니다. 본질적으로 두 강한 참조는 서로 유지되므로 위에서와 같은 문제입니다. 그러나 두 클래스 인스턴스가 아니라 이번에는 서로 유지하는 클래스 인스턴스와 클로저입니다.

Swift는 _클로저 캡처 리스트 \(closure capture list\)_ 로 알려진 이 문제를 위해 해결책을 제공합니다. 그러나 클로저 캡처 리스트로 강한 순환 참조을 끊는 방법에 대해 배우기 전에 이러한 사이클이 어떻게 야기되는지 이해하는 것이 더 유용합니다.

아래의 예시는 `self` 를 참조하는 클로저를 사용할 때 강한 순환 참조이 어떻게 생성될 수 있는지 보여줍니다. 이 예시는 HTML 문서 내에 개별 요소에 대한 간단한 모델을 제공하는 `HTMLElement` 라는 클래스를 정의합니다:

```swift
class HTMLElement {

    let name: String
    let text: String?

    lazy var asHTML: () -> String = {
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name) />"
        }
    }

    init(name: String, text: String? = nil) {
        self.name = name
        self.text = text
    }

    deinit {
        print("\(name) is being deinitialized")
    }

}
```

`HTMLElement` 클래스는 제목 요소에 대한 `"h1"`, 단락 요소에 대한 `"p"`, 또는 개행 요소에 대한 `"br"` 과 같이 요소의 이름을 나타내는 `name` 프로퍼티를 정의합니다. `HTMLElement` 는 HTML 요소 내에 렌더링 될 텍스트를 나타내는 문자열을 설정할 수 있는 옵셔널 `text` 프로퍼티도 정의합니다.

이 간단한 두 프로퍼티 외에도 `HTMLElement` 클래스는 `asHTML` 이라는 지연 프로퍼티 \(lazy property\)를 정의합니다. 이 프로퍼티는 `name` 과 `text` 를 HTML 문자열 조각으로 결합하는 클로저를 참조합니다. `asHTML` 프로퍼티는 `() -> String` 또는 "파라미터가 없고 `String` 값을 반환하는 함수" 타입입니다.

기본적으로 `asHTML` 프로퍼티는 HTML 태그의 문자열 표현을 반환하는 클로저가 할당됩니다. 이 태그는 존재하면 옵셔널 `text` 값을 포함하고 `text` 가 존재하지 않으면 텍스트 콘텐츠는 없습니다. 단락 요소에 대해 클로저는 `text` 프로퍼티가 `"some text"` 또는 `nil` 인지에 따라 `"<p>some text</p>"` 또는 `"<p />"` 을 반환합니다.

`asHTML` 프로퍼티는 인스턴스 메서드와 비슷하게 이름이 지어지고 사용됩니다. 그러나 `asHTML` 은 인스턴스 메서드가 아니라 클로저 프로퍼티 이기 때문에 특정 HTML 요소에 대해 HTML 렌더링을 변경하기 원하면 커스텀 클로저로 `asHTML` 프로퍼티의 기본값을 대체할 수 있습니다.

예를 들어 `asHTML` 프로퍼티는 빈 HTML 태그를 반환하는 표현을 방지하기 위해 `text` 프로퍼티가 `nil` 이면 일부 텍스트를 기본값으로 하는 클로저로 설정할 수 있습니다:

```swift
let heading = HTMLElement(name: "h1")
let defaultText = "some default text"
heading.asHTML = {
    return "<\(heading.name)>\(heading.text ?? defaultText)</\(heading.name)>"
}
print(heading.asHTML())
// Prints "<h1>some default text</h1>"
```

> Note   
> `asHTML` 프로퍼티는 요소가 실제로 일부 HTML 출력 타겟에 대한 문자열 값으로 렌더링 되어야 하는 경우에만 필요하므로 지연 프로퍼티로 선언됩니다. `asHTML` 이 지연 프로퍼티라는 사실은 초기화가 완료되고 `self` 가 존재할 때까지 접근할 수 없으므로 기본 클로저 내에서 `self` 를 참조할 수 있다는 의미입니다.

`HTMLElement` 클래스는 `name` 인수와 필요하면 `text` 인수를 사용하여 새로운 요소를 초기화 하는 단일 이니셜라이저를 제공합니다. 이 클래스는 `HTMLElement` 인스턴스가 할당 해제될 때 메세지를 출력하는 디이니셜라이저도 정의합니다.

다음은 어떻게 `HTMLElement` 클래스가 새로운 인스턴스를 생성하고 출력하기 위해 사용되는지 보여줍니다:

```swift
var paragraph: HTMLElement? = HTMLElement(name: "p", text: "hello, world")
print(paragraph!.asHTML())
// Prints "<p>hello, world</p>"
```

> Note   
> 위의 `paragraph` 변수는 강한 순환 참조의 존재를 보여주기 위해 아래에서 `nil` 로 설정할 수 있으므로 _옵셔널_ `HTMLElement` 로 정의됩니다.

안타깝게도 위에서 작성한 `HTMLElement` 클래스는 `HTMLElement` 인스턴스와 기본 `asHTML` 값으로 사용된 클로저 간에 강한 순환 참조을 생성합니다. 사이클은 다음과 같습니다:

![Closure Reference Cycle 01](closureReferenceCycle01)

인스턴스의 `asHTML` 프로퍼티는 클로저에 대해 강한 참조를 유지합니다. 그러나 클로저는 본문 내에서 `self.name` 과 `self.text` 를 참조하는 방법 처럼 `self` 를 참조하기 때문에 클로저는 `HTMLElement` 인스턴스에 다시 강한 참조를 유지한다는 의미로 `self` 를 _캡처_ 합니다. 둘 사이에 강한 순환 참조이 생성됩니다 \(자세한 내용은 <doc:Closures#캡처값-Capturing-Values> 을 참고 바랍니다\).

> Note   
> 클로저는 여러번 `self` 를 참조하지만 `HTMLElement` 인스턴스에 대해 하나의 강한 참조만 캡처합니다.

`paragraph` 변수를 `nil` 로 설정하고 `HTMLElement` 인스턴스에 대한 강한 참조를 끊으면 강한 순환 참조은 `HTMLElement` 인스턴스와 해당 클로저를 할당 해제하지 않습니다:

```swift
paragraph = nil
```

`HTMLElement` 디이니셜라이저에 메세지는 출력되지 않으며 `HTMLElement` 인스턴스는 할당 해제 되지 않음을 보여줍니다.

## 클로저에 대한 강한 순환 참조 해결 \(Resolving Strong Reference Cycles for Closures\)

클로저의 정의의 부분으로 _캡처 리스트 \(capture list\)_ 를 정의하여 클로저와 클래스 인스턴스 간의 강한 순환 참조을 해결합니다. 캡처 리스트은 클로저의 본문 내에서 하나 이상의 참조 타입을 캡처할 때 사용할 규칙을 정의합니다. 두 클래스 간의 강한 순환 참조과 마찬가지로 캡처된 각 참조를 강한 참조가 아닌 약한 참조 또는 미소유 참조로 선언합니다. 약한 참조 또는 미소유 참조의 적절한 선택은 코드의 다른 부분 간의 관계에 따라 다릅니다.

> Note   
> Swift는 클로저 내에서 `self` 의 멤버를 참조할 때마다 `someProperty` 또는 `someMethod()` 가 아닌 `self.someProperty` 또는 `self.someMethod()` 로 작성해야 합니다. 이것은 실수로 `self` 를 캡처할 수 있는 것을 기억하는데 도움이 됩니다.

### 캡처 리스트 정의 \(Defining a Capture List\)

캡처 리스트에서 각 항목은 `self` 처럼 클래스 인스턴스 또는 `delegate = self.delegate` 와 같은 어떤 값을 초기화된 변수에 대한 참조가 있는 `weak` 또는 `unowned` 키워드와 쌍을 이룹니다. 이 쌍은 콤마로 구분하여 대괄호 내에 작성됩니다.

캡처 리스트은 클로저의 파라미터 리스트 전에 위치하고 반환 타입이 있다면 반환 타입은 파라미터 리스트 다음에 위치합니다:

```swift
lazy var someClosure = {
    [unowned self, weak delegate = self.delegate]
    (index: Int, stringToProcess: String) -> String in
    // closure body goes here
}
```

클로저는 컨텍스트로 부터 유추할 수 있기 때문에 파라미터 리스트 또는 반환 타입을 지정하지 않으면 캡처 리스트는 클로저의 가장 처음에 위치하고 이어서 `in` 키워드가 옵니다:

```swift
lazy var someClosure = {
    [unowned self, weak delegate = self.delegate] in
    // closure body goes here
}
```

### 약한 참조와 미소유 참조 \(Weak and Unowned References\)

클로저와 캡처한 인스턴스가 항상 서로를 참조하고 항상 같은 시간에 할당 해제될 때 클로저의 캡처를 미소유 참조로 정의합니다.

반대로 캡처된 참조가 향후에 `nil` 이 될 때 약한 참조로 캡처를 정의합니다. 약한 참조는 항상 옵셔널 타입이고 참조하는 인스턴스가 할당 해제되면 자동으로 `nil` 이 됩니다. 이를 사용하여 클로저의 본문 내에서 존재하는지 확인할 수 있습니다.

> Note   
> 캡처된 참조가 `nil` 이 되지 않으면 약한 참조보다 미소유 참조로 항상 캡처되어야 합니다.

미소유 참조는 위의 <doc:AutomaticReferenceCounting#클로저에-대한-강한-순환-참조-Strong-Reference-Cycles-for-Closures> 에서 `HTMLElement` 예시에서 강한 순환 참조을 해결하기 위해 사용할 적절한 캡처 방법입니다. 다음은 사이클을 피하기 위해 `HTMLElement` 클래스를 어떻게 작성하는지 보여줍니다:

```swift
class HTMLElement {

    let name: String
    let text: String?

    lazy var asHTML: () -> String = {
        [unowned self] in
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name) />"
        }
    }

    init(name: String, text: String? = nil) {
        self.name = name
        self.text = text
    }

    deinit {
        print("\(name) is being deinitialized")
    }

}
```

`HTMLElement` 의 구현은 `asHTML` 클로저 내에서 캡처 리스트의 추가를 제외하면 이전 구현과 동일합니다. 이러한 경우에 캡처 리스트은 "강한 참조가 아닌 미소유 참조로 `self` 를 캡처합니다" 라는 의미의 `[unowned self]` 입니다.

이전과 같이 `HTMLElement` 인스턴스를 생성하고 출력할 수 있습니다:

```swift
var paragraph: HTMLElement? = HTMLElement(name: "p", text: "hello, world")
print(paragraph!.asHTML())
// Prints "<p>hello, world</p>"
```

다음은 캡처 리스트가 참조에서 어떻게 보이는지 나타냅니다:

![Closure Reference Cycle 02](closureReferenceCycle02)

이번에는 클로저에 의해 `self` 의 캡처는 미소유 참조이고 캡처한 `HTMLElement` 인스턴스를 강하게 유지하지 않습니다. `paragraph` 변수를 `nil` 로 강한 참조를 설정하면 아래 예시에서 디이니셜라이저 메세지를 출력하는 것을 확인했듯이 `HTMLElement` 인스턴스는 할당 해제 됩니다.

```swift
paragraph = nil
// Prints "p is being deinitialized"
```

더 자세한 내용은 <doc:Expressions#캡처-리스트-Capture-Lists> 을 참고바랍니다.

