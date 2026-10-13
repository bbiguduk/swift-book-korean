# Swift 6.4 번역자 보충 (Swift 6.4 Translation Notes)

최신 언어 기능과 영문 원본의 기술적 정정을 확인합니다.

이 페이지는 Swift 6.4 기준의 번역자 보충 자료입니다.
영문 책에 아직 설명되지 않은 기능과
현재 컴파일러의 동작을 설명하기 위해 필요한 정정을 담고 있습니다.
각 절의 공식 Swift Evolution 제안을 함께 참고할 수 있습니다.
책에 설명이 추가된 버전과 언어 기능이 도입된 버전은 서로 다를 수 있습니다.

## 비동기 defer (Asynchronous Defer)

Swift 6.4에서는 비동기 컨텍스트의 `defer` 본문에서
`await`로 비동기 함수를 호출할 수 있습니다.
현재 범위를 벗어날 때 지연된 작업을 암시적으로 기다리고,
해당 작업이 완료된 후에 함수가 반환됩니다.
여러 `defer`의 실행 순서는 기존과 같이 선언 순서의 역순입니다.

```swift
func releaseResource() async {
    // Release a resource asynchronously.
}

func performWork() async {
    defer {
        await releaseResource()
    }
    print("Working")
}
```

`await`를 포함하는 `defer`를 동기 함수에 작성할 수는 없습니다.
클로저처럼 비동기 여부를 추론할 수 있는 컨텍스트에서는
`defer`의 `await`도 클로저가 비동기임을 추론하는 데 사용됩니다.
`defer`는 둘러싸는 범위의 액터 격리를 상속합니다.

`defer` 본문에 작성한 호출의 `await`와 별도로,
범위를 벗어나면서 지연된 작업을 기다리는 암시적 중단 지점이 생깁니다.
따라서 모든 중단 지점이 호출 위치의 명시적 `await`에만 있다는 설명은
이 경우를 포함하도록 한정해야 합니다.
`defer` 자체가 태스크의 취소 상태를 없애 주지는 않습니다.

자세한 내용은 [SE-0493](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0493-defer-async.md)을 참고바랍니다.

## some과 any의 옵셔널 구문 (Optional some and any Syntax)

Swift 6.4에서는 단일 프로토콜의 불투명 타입이나 박싱 프로토콜 타입을
옵셔널로 작성할 때 바깥쪽 괄호를 생략할 수 있습니다.
`some P?`는 `(some P)?`와 같고,
`any P?`는 `(any P)?`와 같습니다.
기존의 괄호를 사용하는 구문도 계속 유효합니다.

```swift
protocol Named {
    var name: String { get }
}

struct Person: Named {
    let name: String
}

let person: any Named? = Person(name: "Taylor")

func makePerson() -> some Named? {
    Optional(Person(name: "Taylor"))
}
```

프로토콜 합성을 사용할 때는 합성 자체를 괄호로 묶습니다.
`any (P & Q)?`와 `(any P & Q)?`는 유효하지만,
`any P & Q?`는 유효하지 않습니다.

자세한 내용은 [SE-0521](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0521-improved-optional-opaque-and-any.md)을 참고바랍니다.

## 경고 제어 속성 (Warning Control Attribute)

`@diagnose`는 특정 선언의 시그니처와 어휘적 범위에서
지정한 경고 그룹의 진단 방식을 조정합니다.
첫 번째 인자는 경고 그룹 이름이며,
`as:` 인자는 `error`, `warning`, `ignored` 중 하나입니다.
선택적인 `reason:` 인자에는 문자열 보간이 없는 문자열 리터럴을 작성합니다.

```swift
@available(*, deprecated, message: "Use currentAPI() instead.")
func legacyAPI() {}

@diagnose(DeprecatedDeclaration, as: ignored, reason: "Compatibility bridge")
func compatibilityBridge() {
    legacyAPI()
}
```

이 속성은 지정한 그룹의 경고에만 영향을 줍니다.
컴파일 오류를 무시하도록 바꾸는 기능은 아닙니다.
중첩된 선언은 둘러싸는 범위의 설정을 상속하며,
해당 경고 그룹에 별도 `@diagnose`를 작성하여 설정을 조정할 수 있습니다.

SE-0522는 Swift 6.4에 구현되었습니다.
사용하는 툴체인이 실험적 기능 활성화를 요구하는 경우에는
`-enable-experimental-feature SourceWarningControl`을 사용합니다.

자세한 내용은 [SE-0522](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0522-source-warning-control.md)을 참고바랍니다.

## 모듈 선택자 (Module Selectors)

`ModuleName::Name` 구문을 사용하면
지정한 모듈에 속하는 선언을 명확하게 참조할 수 있습니다.
여러 모듈에 같은 이름의 선언이 있거나,
모듈과 같은 이름의 타입이 모듈 이름을 가리는 경우에 유용합니다.

```swift
let value: Swift::Int = 42
```

점(`.`)을 사용하는 일반적인 멤버 접근과 달리,
`::` 앞의 이름은 모듈을 선택하는 데 사용됩니다.
이 기능의 Swift Evolution 구현 버전은 Swift 6.3이며,
Swift 6.4 릴리스 소개에서도 다루고 있습니다.

자세한 내용은 [SE-0491](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0491-module-selectors.md)을 참고바랍니다.

## 정리 작업의 취소 차단 (Cancellation Shields for Cleanup)

태스크 취소는 협조적으로 처리됩니다.
필수 정리 작업이 내부에서 취소 여부를 검사하고 실행을 건너뛰는 경우,
`withTaskCancellationShield`를 사용하여
그 작업이 둘러싸는 태스크의 취소 상태를 관찰하지 않도록 보호할 수 있습니다.
이 API에는 동기 클로저와 비동기 클로저를 받는 형태가 있습니다.

```swift
func flushMetrics() async {
    guard !Task.isCancelled else { return }
    print("Flushing metrics")
}

func processResource() async {
    defer {
        await withTaskCancellationShield {
            await flushMetrics()
        }
    }
    print("Processing resource")
}
```

차단 범위에서는 `Task.isCancelled`와 `Task.checkCancellation()`이
둘러싸는 태스크의 취소를 관찰하지 않습니다.
이 범위에서 생성한 구조화된 자식 태스크에도
둘러싸는 태스크의 취소가 자동으로 전파되지 않습니다.
그러나 태스크의 취소 자체를 취소하거나 되돌리는 것은 아니며,
차단 범위를 벗어나면 원래 취소 상태를 다시 관찰합니다.
자식 태스크나 태스크 그룹을 명시적으로 취소하는 동작까지 막지는 않습니다.

자세한 내용은 [SE-0504](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0504-task-cancellation-shields.md)를 참고바랍니다.

## 박싱 프로토콜과 제네릭 호출 (Boxed Protocols and Generic Calls)

박싱 프로토콜 타입 자체가 해당 프로토콜을 준수하지 않는다는 사실과
그 값을 제네릭 함수에 인자로 전달할 수 있는지는 구분해야 합니다.
Swift는 지원하는 호출 위치에서 existential을 암시적으로 열어,
박스에 저장된 구체적인 타입을 제네릭 매개변수에 바인딩할 수 있습니다.

따라서 <doc:OpaqueTypes>의 `protoFlip(_:)` 예제에서
`protoFlip(protoFlip(smallTriangle))` 호출은
현재 Swift 6.4의 Swift 6 언어 모드에서 유효합니다.
반면 서로 다른 박스의 구체 타입이 같다고 보장할 수 없는 연산에는
여전히 제약이 있습니다.
모든 existential 사용이 모든 제네릭 컨텍스트에서 가능한 것은 아닙니다.

연관 타입이 있는 프로토콜도
명시적 박싱 프로토콜 타입인 `any Container`로 반환할 수 있습니다.
`some Container`를 반환하는 경우와 비교하면,
반환값의 구체적인 타입과 연관 타입 정보를 보존하는 정도가 다릅니다.

자세한 내용은 [SE-0352](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0352-implicit-open-existentials.md)와
[SE-0309](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0309-unlock-existential-types-for-all-protocols.md)를 참고바랍니다.

## 원시 식별자의 형식 문법 (Formal Grammar for Raw Identifiers)

현재 영문 원본의 `raw-identifier-character` 생산 규칙은
원시 식별자에 포함할 수 없는 문자를 일부만 나열합니다.
<doc:LexicalStructure#식별자-Identifiers> 본문의 금지 문자 목록과
공백만 또는 연산자 문자만으로 된 이름을 허용하지 않는 조건을 함께 적용해야 합니다.
문법 요약의 짧은 생산 규칙만으로
본문에서 금지한 탭이나 유니코드 공백이 허용된다고 해석하면 안 됩니다.

자세한 내용은 [SE-0451](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0451-escaped-identifiers.md)을 참고바랍니다.
