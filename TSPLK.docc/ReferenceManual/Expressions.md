# 표현식 (Expressions)

값에 접근, 수정, 할당합니다.

Swift에는 네 종류의 표현식이 있습니다:
접두 표현식(prefix expressions), 중위 표현식(infix expressions), 기본 표현식(primary expressions), 접미 표현식(postfix expressions)입니다.
표현식을 평가하면 값을 반환하거나
부작용(side effect)를 일으키거나 둘 다 발생합니다.

접두 표현식과 중위 표현식은
더 작은 표현식에 연산자를 적용할 수 있습니다.
기본 표현식은 개념적으로 가장 간단한 표현식이며
값에 접근하는 방법을 제공합니다.
접미 표현식은
접두 표현식과 중위 표현식과 같이
함수 호출이나 멤버 접근과 같은 접미사를 사용하여
더 복잡한 표현식을 작성할 수 있습니다.
표현식의 각 종류는 아래 섹션에서
자세하게 설명합니다.

> Grammar of an expression:
>
> *expression* → *try-operator*_?_ *await-operator*_?_ *prefix-expression* *infix-expressions*_?_

## 접두 표현식 (Prefix Expressions)

*접두 표현식(Prefix expressions)*은
옵셔널 접두 연산자(prefix operator)와 표현식을 결합합니다.
접두 연산자는 하나의 인자를 받으며
그 인자는 연산자 뒤에 오는 표현식입니다.

이 연산자의 동작에 대한 자세한 설명은
<doc:BasicOperators>와 <doc:AdvancedOperators>를 참고바랍니다.

Swift 표준 라이브러리에 의해 제공되는 연산자의 자세한 설명은
[연산자 선언 (Operator Declarations)](https://developer.apple.com/documentation/swift/operator_declarations)을 참고바랍니다.

> Grammar of a prefix expression:
>
> *prefix-expression* → *prefix-operator*_?_ *postfix-expression* \
> *prefix-expression* → *in-out-expression*

### In-Out 표현식 (In-Out Expression)

*in-out 표현식(in-out expression)*은
in-out 인자로 함수 호출 표현식에 전달되는
변수를 표시합니다.

```swift
&<#expression#>
```

in-out 파라미터에 대한 설명과 예시는
<doc:Functions#In-Out-파라미터-In-Out-Parameters>를 참고바랍니다.

in-out 표현식은
<doc:Expressions#포인터-타입으로-암시적-변환-Implicit-Conversion-to-a-Pointer-Type>에서 설명한대로
포인터가 필요한 컨텍스트에서
포인터가 아닌 인자(non-pointer argument)를 제공할 때도 사용합니다.

> Grammar of an in-out expression:
>
> *in-out-expression* → **`&`** *primary-expression*

### Try 연산자 (Try Operator)

*try 표현식(try expression)*은 `try` 연산자 다음에
오류를 던질 수 있는 표현식으로 구성되어 있습니다.
형식은 다음과 같습니다:

```swift
try <#expression#>
```

`try` 표현식의 값은 위 형식의 *표현식(expression)*과 같습니다.

*옵셔널 try 표현식(optional-try expression)*은 `try?` 연산자 다음에
오류를 던질 수 있는 표현식으로 구성되어 있습니다.
형식은 다음과 같습니다:

```swift
try? <#expression#>
```

위 형식의 *표현식(expression)*에서 오류를 던지지 않는다면,
옵셔널 try 표현식의 값은
위 형식의 *표현식(expression)*의 값을 포함하는 옵셔널입니다.
그렇지 않으면 옵셔널 try 표현식의 값은 `nil`입니다.

*강제 try 표현식(forced-try expression)*은 `try!` 연산자 다음에
오류를 던질 수 있는 표현식으로 구성되어 있습니다.
형식은 다음과 같습니다:

```swift
try! <#expression#>
```

강제 try 표현식의 값은 위 형식의 *표현식(expression)*의 값입니다.
위 형식의 *표현식(expression)*이 오류를 던지면
런타임 오류가 발생합니다.

중위 연산자의 왼쪽 피연산자에
`try`, `try?`, `try!`가 표시되어 있으면,
해당 연산자는 중위 표현식 전체에 적용됩니다.
즉, 괄호를 사용하여 연산자의 적용 범위를 지정할 수 있습니다.

```swift
// try applies to both function calls
sum = try someThrowingFunction() + anotherThrowingFunction()

// try applies to both function calls
sum = try (someThrowingFunction() + anotherThrowingFunction())

// Error: try applies only to the first function call
sum = (try someThrowingFunction()) + anotherThrowingFunction()
```

<!--
  - test: `placement-of-try`

  ```swifttest
  >> func someThrowingFunction() throws -> Int { return 10 }
  >> func anotherThrowingFunction() throws -> Int { return 5 }
  >> var sum = 0
  // try applies to both function calls
  -> sum = try someThrowingFunction() + anotherThrowingFunction()
  ---
  // try applies to both function calls
  -> sum = try (someThrowingFunction() + anotherThrowingFunction())
  ---
  // Error: try applies only to the first function call
  -> sum = (try someThrowingFunction()) + anotherThrowingFunction()
  !$ error: call can throw but is not marked with 'try'
  !! sum = (try someThrowingFunction()) + anotherThrowingFunction()
  !!                                      ^~~~~~~~~~~~~~~~~~~~~~~~~
  !$ note: did you mean to use 'try'?
  !! sum = (try someThrowingFunction()) + anotherThrowingFunction()
  !!                                      ^
  !!                                      try
  !$ note: did you mean to handle error as optional value?
  !! sum = (try someThrowingFunction()) + anotherThrowingFunction()
  !!                                      ^
  !!                                      try?
  !$ note: did you mean to disable error propagation?
  !! sum = (try someThrowingFunction()) + anotherThrowingFunction()
  !!                                      ^
  !!                                      try!
  ```
-->

`try` 표현식은 중위 연산자의 오른쪽 피연산자에 나타날 수 없지만,
중위 연산자가 할당 연산자이거나
`try` 표현식이 괄호로 묶여있는 경우에는 예외입니다.

<!--
  - test: `try-on-right`

  ```swifttest
  >> func someThrowingFunction() throws -> Int { return 10 }
  >> var sum = 0
  -> sum = 7 + try someThrowingFunction() // Error
  !$ error: 'try' cannot appear to the right of a non-assignment operator
  !! sum = 7 + try someThrowingFunction() // Error
  !!           ^
  -> sum = 7 + (try someThrowingFunction()) // OK
  ```
-->

표현식에 `try`와 `await` 연산자 모두 포함될 경우,
`try` 연산자를 먼저 작성합니다.

<!--
  The "try await" ordering is also part of the grammar for 'expression',
  but it's important enough to be worth re-stating in prose.
-->

더 자세한 정보와 `try`, `try?`, `try!` 사용법에 대한 예시는
<doc:ErrorHandling>를 참고바랍니다.

> Grammar of a try expression:
>
> *try-operator* → **`try`** | **`try`** **`?`** | **`try`** **`!`**

## Await 연산자 (Await Operator)

*await 표현식(await expression)*은 `await` 연산자 다음에
비동기 동작의 결과를 사용하는 표현식으로 구성되어 있습니다.
형식은 다음과 같습니다:

```swift
await <#expression#>
```

`await` 표현식의 값은 위 형식의 *표현식(expression)*의 값입니다.

`await`로 표시된 표현식은 *잠재적 중단 지점(potential suspension point)*이라 합니다.
비동기 함수의 실행은
`await`로 표시된 각 표현식에서 일시 중단될 수 있습니다.
반대로
비동기 코드의 실행은 이런 지점이 아닌 다른 시점에서는 중단되지 않습니다.
이는 잠재적 중단 지점 사이의 코드가
일시적으로 중단을 깨야하는 상태를 안전하게 업데이트 할 수 있고
다음 잠재적 중단 지점 이전에
해당 작업을 완료하기만 하면 됩니다.

`await` 표현식은 `async(priority:operation:)` 함수에 전달된 후행 클로저와 같이
비동기 컨텍스트 내에서만 사용할 수 있습니다.
`defer` 구문의 본문이나
동기 함수 타입의 자동 클로저(autoclosure) 내에서는 사용할 수 없습니다.

중위 연산자(infix operator)의 왼쪽 피연산자에
`await` 연산자로 표시하면
해당 연산자는 전체 중위 표현식에 적용됩니다.
즉, 괄호를 사용하여
연산자의 적용 범위를 명시할 수 있습니다.

```swift
// await applies to both function calls
sum = await someAsyncFunction() + anotherAsyncFunction()

// await applies to both function calls
sum = await (someAsyncFunction() + anotherAsyncFunction())

// Error: await applies only to the first function call
sum = (await someAsyncFunction()) + anotherAsyncFunction()
```

<!--
  - test: `placement-of-await`

  ```swifttest
  >> func someAsyncFunction() async -> Int { return 10 }
  >> func anotherAsyncFunction() async -> Int { return 5 }
  >> func f() async {
  >> var sum = 0
  // await applies to both function calls
  -> sum = await someAsyncFunction() + anotherAsyncFunction()
  ---
  // await applies to both function calls
  -> sum = await (someAsyncFunction() + anotherAsyncFunction())
  ---
  // Error: await applies only to the first function call
  -> sum = (await someAsyncFunction()) + anotherAsyncFunction()
  >> _ = sum  // Suppress irrelevant written-but-not-read warning
  >> }
  !$ error: expression is 'async' but is not marked with 'await'
  !! sum = (await someAsyncFunction()) + anotherAsyncFunction()
  !! ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  !! await
  !$ note: call is 'async'
  !! sum = (await someAsyncFunction()) + anotherAsyncFunction()
  !! ^
  ```
-->

`await` 표현식은 중위 연산자의 우항에 나타날 수 없지만,
중위 연산자가 할당 연산자이거나
`await` 표현식이 괄호로 묶인 경우는 예외입니다.

<!--
  - test: `await-on-right`

  ```swifttest
  >> func f() async {
  >> func someAsyncFunction() async -> Int { return 10 }
  >> var sum = 0
  >> sum = 7 + await someAsyncFunction()    // Error
  !$ error: 'await' cannot appear to the right of a non-assignment operator
  !! sum = 7 + await someAsyncFunction()    // Error
  !! ^
  >> sum = 7 + (await someAsyncFunction())  // OK
  >> _ = sum  // Suppress irrelevant written-but-not-read warning
  >> }
  ```
-->

표현식에 `await`와 `try` 연산자가 모두 포함되면,
`try` 연산자를 먼저 작성합니다.

<!--
  The "try await" ordering is also part of the grammar for 'expression',
  but it's important enough to be worth re-stating in prose.
-->

> Grammar of an await expression:
>
> *await-operator* → **`await`**

## 중위 표현식 (Infix Expressions)

*중위 표현식(Infix expressions)*은
중위 이항 연산자(infix binary operator)의
왼쪽과 오른쪽 인자로 사용되는 표현식을 결합합니다.
형식은 다음과 같습니다:

```swift
<#left-hand argument#> <#operator#> <#right-hand argument#>
```

이 연산자의 동작에 대한 자세한 설명은
<doc:BasicOperators>와 <doc:AdvancedOperators>를 참고바랍니다.

Swift 표준 라이브러리에 의해 제공되는 연산자에 대한 자세한 내용은
[연산자 선언 (Operator Declarations)](https://developer.apple.com/documentation/swift/operator_declarations)을 참고바랍니다.

<!--
  You have essentially expression sequences here, and within it are
  parts of the expressions.  We're calling them "expressions" even
  though they aren't what we ordinarily think of as expressions.  We
  have this two-phase thing where we do the expression sequence parsing
  which gives a rough parse tree.  Then after name binding we know
  operator precedence and we do a second phase of parsing that builds
  something that's a more traditional tree.
-->

> Note: 구문 분석 시,
> 중위 연산자로 구성된 표현식은
> 평평한 목록으로 표현됩니다.
> 이 목록은 연산자 우선순위를 적용하여
> 트리로 변환합니다.
> 예를 들어 표현식 `2 + 3 * 5`는
> 처음에는 다섯 개의 항목 `2`, `+`, `3`, `*`, `5`의
> 평평한 목록으로 이해합니다.
> 이 과정은 표현식을 (2 + (3 * 5)) 트리로 변환합니다.

> Grammar of an infix expression:
>
> *infix-expression* → *infix-operator* *prefix-expression* \
> *infix-expression* → *assignment-operator* *try-operator*_?_ *await-operator*_?_ *prefix-expression* \
> *infix-expression* → *conditional-operator* *try-operator*_?_ *await-operator*_?_ *prefix-expression* \
> *infix-expression* → *type-casting-operator* \
> *infix-expressions* → *infix-expression* *infix-expressions*_?_

### 할당 연산자 (Assignment Operator)

*할당 연산자(assignment operator)*는
주어진 표현식에 대해 새로운 값을 설정합니다.
다음의 형식을 가집니다:

```swift
<#expression#> = <#value#>
```

위 형식의 *표현식(expression)*의 값은
위 형식의 *값(value)*을 평가하여 얻어진 값으로 설정합니다.
위 형식의 *표현식(expreesion)*이 튜플이면,
위 형식의 *값(value)*은 튜플이어야 하고
동일한 수의 요소를 가져야 합니다.
(중첩된 튜플도 가능합니다.)
위 형식의 *값(value)*에서
위 형식의 *표현식(expression)*에 할당됩니다.
예를 들어:

```swift
(a, _, (b, c)) = ("test", 9.45, (12, 3))
// a is "test", b is 12, c is 3, and 9.45 is ignored
```

<!--
  - test: `assignmentOperator`

  ```swifttest
  >> var (a, _, (b, c)) = ("test", 9.45, (12, 3))
  -> (a, _, (b, c)) = ("test", 9.45, (12, 3))
  /> a is \"\(a)\", b is \(b), c is \(c), and 9.45 is ignored
  </ a is "test", b is 12, c is 3, and 9.45 is ignored
  ```
-->

할당 연산자는 값을 반환하지 않습니다.

> Grammar of an assignment operator:
>
> *assignment-operator* → **`=`**

### 삼항 조건 연산자 (Ternary Conditional Operator)

*삼항 조건 연산자(ternary conditional operator)*는 조건의 값을 기반으로
주어진 두 개의 값 중 하나로 나타냅니다.
다음의 형식을 가집니다:

```swift
<#condition#> ? <#expression used if true#> : <#expression used if false#>
```

위 형식의 *조건(condition)*이 `true`이면,
조건부 연산자는 첫 번째 표현식을 평가하고
해당 값을 반환합니다.
그렇지 않으면 두 번째 표현식을 평가하고
해당 값을 반환합니다.
사용하지 않은 표현식은 평가되지 않습니다.

삼항 조건 연산자 사용에 대한 예시는
<doc:BasicOperators#삼항-조건-연산자-Ternary-Conditional-Operator>를 참고바랍니다.

> Grammar of a conditional operator:
>
> *conditional-operator* → **`?`** *expression* **`:`**

### 타입 캐스팅 연산자 (Type-Casting Operators)

타입 캐스팅 연산자(type-casting operators)는 네 가지가 있습니다:
`is` 연산자,
`as` 연산자,
`as?` 연산자,
`as!` 연산자입니다.

다음의 형식을 가지고 있습니다:

```swift
<#expression#> is <#type#>
<#expression#> as <#type#>
<#expression#> as? <#type#>
<#expression#> as! <#type#>
```

`is` 연산자는 런타임 시에 위 형식의 *표현식(expression)*이
지정된 위 형식의 *타입(type)*으로 캐스팅될 수 있는지 확인합니다.
위 형식의 *표현식(expression)*이 지정된 위 형식의 *타입(type)*으로 캐스팅될 수 있으면 `true`를 반환하고;
그렇지 않으면 `false`를 반환합니다.

<!--
  - test: `triviallyTrueIsAndAs`

  ```swifttest
  -> assert("hello" is String)
  -> assert(!("hello" is Int))
  !$ warning: 'is' test is always true
  !! assert("hello" is String)
  !!                ^
  !$ warning: cast from 'String' to unrelated type 'Int' always fails
  !! assert(!("hello" is Int))
  !!          ~~~~~~~ ^  ~~~
  ```
-->

<!--
  - test: `is-operator-tautology`

  ```swifttest
  -> class Base {}
  -> class Subclass: Base {}
  -> var s = Subclass()
  -> var b = Base()
  ---
  -> assert(s is Base)
  !$ warning: 'is' test is always true
  !! assert(s is Base)
  !!          ^
  ```
-->

`as` 연산자는
업캐스팅(upcasting)이나 브리징(bridging)과 같이
컴파일 시 변환이 항상 성공하는 경우에
캐스팅을 수행합니다.
업캐스팅(Upcasting)을 사용하면 중간 변수를 사용하지 않고
표현식을 해당 타입의 상위 타입 인스턴스로 사용할 수 있습니다.
다음 예시의 방식은 둘 다 동일합니다:

```swift
func f(_ any: Any) { print("Function for Any") }
func f(_ int: Int) { print("Function for Int") }
let x = 10
f(x)
// Prints "Function for Int"

let y: Any = x
f(y)
// Prints "Function for Any"

f(x as Any)
// Prints "Function for Any"
```

<!--
  - test: `explicit-type-with-as-operator`

  ```swifttest
  -> func f(_ any: Any) { print("Function for Any") }
  -> func f(_ int: Int) { print("Function for Int") }
  -> let x = 10
  -> f(x)
  <- Function for Int
  ---
  -> let y: Any = x
  -> f(y)
  <- Function for Any
  ---
  -> f(x as Any)
  <- Function for Any
  ```
-->

브리징(Bridging)을 사용하면
`String`과 같은 Swift 표준 라이브러리 타입을
`NSString`과 같은 해당 Foundation 타입으로 사용할 수 있게 해주고
새로운 인스턴스를 생성할 필요가 없습니다.
브리징에 대한 자세한 설명은
[Foundation 타입 동작 (Working with Foundation Types)](https://developer.apple.com/documentation/swift/imported_c_and_objective_c_apis/working_with_foundation_types)을 참고바랍니다.

`as?` 연산자는
위 형식의 *표현식(expression)*을
지정한 위 형식의 *타입(type)*으로 조건부로 캐스팅을 수행합니다.
`as?` 연산자는 지정한 위 형식의 *타입(type)*의 옵셔널로 반환합니다.
런타임 시 캐스팅이 성공하면,
위 형식의 *표현식(expression)*의 값은 옵셔널로 래핑하여 반환됩니다;
그렇지 않으면 `nil`이 반환됩니다.
지정된 위 형식의 *타입(type)*으로 캐스팅이
항상 실패하거나 항상 성공이 보장되면
컴파일 시 오류가 발생합니다.

`as!` 연산자는 위 형식의 *표현식(expression)*을 지정한 위 형식의 *타입(type)*으로 강제 캐스팅을 수행합니다.
`as!` 연산자는 옵셔널 타입이 아닌 지정한 위 형식의 *타입(type)*의 값을 반환합니다.
캐스팅이 실패하면 런타임 오류가 발생합니다.
`x as! T`의 동작은 `(x as? T)!`의 동작과 동일합니다.

타입 캐스팅에 대한 자세한 내용과
타입 캐스팅 연산자 사용에 대한 예시는
<doc:TypeCasting>을 참고바랍니다.

> Grammar of a type-casting operator:
>
> *type-casting-operator* → **`is`** *type* \
> *type-casting-operator* → **`as`** *type* \
> *type-casting-operator* → **`as`** **`?`** *type* \
> *type-casting-operator* → **`as`** **`!`** *type*

## 기본 표현식 (Primary Expressions)

*기본 표현식(Primary expressions)*은
표현식의 가장 기본입니다.
표현식 자체로 사용될 수 있으며,
접두 표현식, 중위 표현식, 접미 표현식을 만들기 위해
다른 토큰과 결합될 수 있습니다.

> Grammar of a primary expression:
>
> *primary-expression* → *identifier* *generic-argument-clause*_?_ \
> *primary-expression* → *literal-expression* \
> *primary-expression* → *self-expression* \
> *primary-expression* → *superclass-expression* \
> *primary-expression* → *conditional-expression* \
> *primary-expression* → *closure-expression* \
> *primary-expression* → *parenthesized-expression* \
> *primary-expression* → *tuple-expression* \
> *primary-expression* → *implicit-member-expression* \
> *primary-expression* → *wildcard-expression* \
> *primary-expression* → *macro-expansion-expression* \
> *primary-expression* → *key-path-expression* \
> *primary-expression* → *selector-expression* \
> *primary-expression* → *key-path-string-expression*

<!--
  NOTE: One reason for breaking primary expressions out of postfix
  expressions is for exposition -- it makes it easier to organize the
  prose surrounding the production rules.
-->

<!--
  TR: Is a generic argument clause allowed
  after an identifier in expression context?
  It seems like that should only occur when an identifier
  is a *type* identifier.
-->

### 리터럴 표현식 (Literal Expression)

*리터럴 표현식(literal expression)*은
일반 리터럴(문자열이나 숫자 등),
배열이나 딕셔너리 리터럴,
플레이그라운드 리터럴로 구성됩니다:

> Note:
> Swift 5.9 이전에는
> 다음의 특수 리터럴이 인식되었습니다:
> `#column`,
> `#dsohandle`,
> `#fileID`,
> `#filePath`,
> `#file`,
> `#function`,
> `#line`.
> 이것은 현재 Swift 표준 라이브러리에 매크로(macro)로 구현됩니다:
> [`column()`](https://developer.apple.com/documentation/swift/column()),
> [`dsohandle()`](https://developer.apple.com/documentation/swift/dsohandle()),
> [`fileID()`](https://developer.apple.com/documentation/swift/fileID()),
> [`filePath()`](https://developer.apple.com/documentation/swift/filePath()),
> [`file()`](https://developer.apple.com/documentation/swift/file()),
> [`function()`](https://developer.apple.com/documentation/swift/function()),
> [`line()`](https://developer.apple.com/documentation/swift/line()).

<!--
  - test: `pound-file-flavors`

  ```swifttest
  >> print(#file == #filePath)
  << true
  >> print(#file == #fileID)
  << false
  ```
-->

*배열 리터럴(array literal)*은
순서가 있는 값의 컬렉션입니다.
형식은 다음과 같습니다:

```swift
[<#value 1#>, <#value 2#>, <#...#>]
```

배열의 마지막 표현식 뒤에 콤마를 추가할 수 있습니다.
배열 리터럴의 값은 `[T]` 타입이고,
여기서 `T`는 표현식 내의 타입을 나타냅니다.
여러 타입의 표현식이 있는 경우
`T`는 가장 가까운 공통 상위 타입(supertype)입니다.
빈 배열 리터럴은
빈 대괄호 쌍을 사용하여 작성하고 지정된 타입의 빈 배열을 생성할 수 있습니다.

```swift
var emptyArray: [Double] = []
```

<!--
  - test: `array-literal-brackets`

  ```swifttest
  -> var emptyArray: [Double] = []
  ```
-->

*딕셔너리 리터럴(dictionary literal)*은
순서가 없는 키-값 쌍의 컬렉션입니다.
형식은 다음과 같습니다:

```swift
[<#key 1#>: <#value 1#>, <#key 2#>: <#value 2#>, <#...#>]
```

딕셔너리에 마지막 표현식 뒤에 콤마를 추가할 수 있습니다.
딕셔너리 리터럴의 값은 `[Key: Value]` 타입을 가지고,
여기서 `Key`는 키 표현식의 타입이고
`Value`는 값 표현식의 타입입니다.
여러 타입의 표현식이 있는 경우
`Key`와 `Value`는 해당 값에 대해
가장 가까운 공통 상위 타입입니다.
빈 딕셔너리 리터럴은
빈 배열 리터럴과 구분하기 위해
대괄호 쌍 내에 콜론(`[:]`)을 작성합니다.
빈 딕셔너리 리터럴을
지정한 키와 값 타입으로 빈 딕셔너리 리터럴을 생성할 수 있습니다.

```swift
var emptyDictionary: [String: Double] = [:]
```

<!--
  - test: `dictionary-literal-brackets`

  ```swifttest
  -> var emptyDictionary: [String: Double] = [:]
  ```
-->

*플레이그라운드 리터럴(playground literal)*은
Xcode에서 색상, 파일, 이미지를
대화형으로 나타낼 수 있는 표현식입니다.
Xcode 외부의 일반 텍스트에서 플레이그라운드 리터럴은
특수 리터럴 구문을 사용하여 표현합니다.

Xcode에서 플레이그라운드 리터럴 사용에 대한 정보는
Xcode Help의
[색상, 파일, 이미지 리터럴 추가하기 (Add a color, file, or image literal)](https://help.apple.com/xcode/mac/current/#/dev4c60242fc)를 참고바랍니다.

> Grammar of a literal expression:
>
> *literal-expression* → *literal* \
> *literal-expression* → *array-literal* | *dictionary-literal* | *playground-literal*
>
> *array-literal* → **`[`** *array-literal-items*_?_ **`]`** \
> *array-literal-items* → *array-literal-item* **`,`**_?_ | *array-literal-item* **`,`** *array-literal-items* \
> *array-literal-item* → *expression*
>
> *dictionary-literal* → **`[`** *dictionary-literal-items* **`]`** | **`[`** **`:`** **`]`** \
> *dictionary-literal-items* → *dictionary-literal-item* **`,`**_?_ | *dictionary-literal-item* **`,`** *dictionary-literal-items* \
> *dictionary-literal-item* → *expression* **`:`** *expression*
>
> *playground-literal* → **`#colorLiteral`** **`(`** **`red`** **`:`** *expression* **`,`** **`green`** **`:`** *expression* **`,`** **`blue`** **`:`** *expression* **`,`** **`alpha`** **`:`** *expression* **`)`** \
> *playground-literal* → **`#fileLiteral`** **`(`** **`resourceName`** **`:`** *expression* **`)`** \
> *playground-literal* → **`#imageLiteral`** **`(`** **`resourceName`** **`:`** *expression* **`)`**

### Self 표현식 (Self Expression)

`self` 표현식은 현재 타입
또는 해당 타입의 인스턴스를 명시적으로 참조하는 표현입니다.
형식은 다음과 같습니다:

```swift
self
self.<#member name#>
self[<#subscript index#>]
self(<#initializer arguments#>)
self.init(<#initializer arguments#>)
```

<!--
  TODO: Come back and explain the second to last form (i.e., self(arg: value)).
-->

이니셜라이저, 서브스크립트, 인스턴스 메서드에서 `self`는
해당 코드가 속한 타입의 인스턴스를 참조합니다.
타입 메서드에서 `self`는 해당 코드가 속한 타입 자체를 참조합니다.

`self` 표현식은 멤버에 접근할 때
범위를 지정하는데 사용되고
함수 파라미터와 같이
범위에 같은 이름의 다른 변수가 있을 때 혼돈을 방지하기 위해 사용됩니다.
예를 들어:

```swift
class SomeClass {
    var greeting: String
    init(greeting: String) {
        self.greeting = greeting
    }
}
```

<!--
  - test: `self-expression`

  ```swifttest
  -> class SomeClass {
         var greeting: String
         init(greeting: String) {
             self.greeting = greeting
         }
     }
  ```
-->

값 타입의 mutating 메서드에서
해당 값 타입의 새 인스턴스를 `self`에 할당할 수 있습니다.
예를 들어:

```swift
struct Point {
    var x = 0.0, y = 0.0
    mutating func moveBy(x deltaX: Double, y deltaY: Double) {
        self = Point(x: x + deltaX, y: y + deltaY)
    }
}
```

<!--
  - test: `self-expression`

  ```swifttest
  -> struct Point {
        var x = 0.0, y = 0.0
        mutating func moveBy(x deltaX: Double, y deltaY: Double) {
           self = Point(x: x + deltaX, y: y + deltaY)
        }
     }
  >> var somePoint = Point(x: 1.0, y: 1.0)
  >> somePoint.moveBy(x: 2.0, y: 3.0)
  >> print("The point is now at (\(somePoint.x), \(somePoint.y))")
  << The point is now at (3.0, 4.0)
  ```
-->

<!-- Apple Books screenshot begins here. -->

> Grammar of a self expression:
>
> *self-expression* → **`self`** | *self-method-expression* | *self-subscript-expression* | *self-initializer-expression*
>
> *self-method-expression* → **`self`** **`.`** *identifier* \
> *self-subscript-expression* → **`self`** **`[`** *function-call-argument-list* **`]`** \
> *self-initializer-expression* → **`self`** **`.`** **`init`**

### 상위 클래스 표현식 (Superclass Expression)

*상위 클래스 표현식(superclass expression)*을 사용하면
클래스의 상위 클래스와 상호작용할 수 있습니다.
다음 형식 중 하나를 사용합니다:

```swift
super.<#member name#>
super[<#subscript index#>]
super.init(<#initializer arguments#>)
```

첫 번째 형식은 상위 클래스의 멤버에 접근하기 위해 사용합니다.
두 번째 형식은 상위 클래스의 서브스크립트 구현에 접근하기 위해 사용합니다.
세 번째 형식은 상위 클래스의 이니셜라이저에 접근하기 위해 사용합니다.

하위 클래스는
멤버, 서브스크립트, 이니셜라이저 구현 내에서
상위 클래스 표현식을 사용하여 상위 클래스의 구현을 활용할 수 있습니다.

> Grammar of a superclass expression:
>
> *superclass-expression* → *superclass-method-expression* | *superclass-subscript-expression* | *superclass-initializer-expression*
>
> *superclass-method-expression* → **`super`** **`.`** *identifier* \
> *superclass-subscript-expression* → **`super`** **`[`** *function-call-argument-list* **`]`** \
> *superclass-initializer-expression* → **`super`** **`.`** **`init`**

### 조건 표현식 (Conditional Expression)

*조건 표현식(conditional expression)*은 조건의 값을 기반으로
여러 주어진 값 중 하나로 평가됩니다.
다음의 형식을 가집니다:

```swift
if <#condition 1#> {
   <#expression used if condition 1 is true#>
} else if <#condition 2#> {
   <#expression used if condition 2 is true#>
} else {
   <#expression used if both conditions are false#>
}

switch <#expression#> {
case <#pattern 1#>:
    <#expression 1#>
case <#pattern 2#> where <#condition#>:
    <#expression 2#>
default:
    <#expression 3#>
}
```

조건 표현식은
`if` 구문이나 `switch` 구문과 같은 동작과 구문을 가지지만,
아래에서 설명하는 다른점을 가집니다.

조건 표현식은 다음 컨텍스트에서만 나타납니다:

  - 변수에 할당된 값.
  - 변수나 상수 선언에서 초기값.
  - `throw` 표현식에서 발생시키는 오류.
  - 함수, 클로저, 프로퍼티 getter의 반환된 값.
  - 조건 표현식의 분기 안에서의 값.

조건 표현식의 분기는
조건에 상관없이
값을 생성하므로 완벽합니다.
이것은 각 `if` 분기는 적절한 `else` 분기가 필요합니다. 

각 분기는 하나의 표현식만 포함되어야 하고,
이 표현식은 해당 조건이 참일 때
그 분기의 결과 값으로 사용하거나
`throw` 구문이나
반환하지 않는 함수 호출로 구성될 수 있습니다.

각 분기는 같은 타입의 값을 반환해야 합니다.
각 분기의 타입 검사는 독립적이기 때문에,
분기가 다른 종류의 리터럴을 포함하거나
분기의 값이 `nil`과 같을 때
값의 타입을 명시해야 합니다.
이 정보를 제공해야 할 때,
할당되는 결과 변수에 타입 명시를 추가하거나
분기의 값에 `as` 변환을 추가합니다.

```swift
let number: Double = if someCondition { 10 } else { 12.34 }
let number = if someCondition { 10 as Double } else { 12.34 }
```

결과 빌더(result builder) 내에서
조건 표현식은
변수나 상수의 초기값으로만 사용할 수 있습니다.
이것은 결과 빌더 내에서 `if`나 `switch`를
변수나 상수 선언이 아닌 위치에서 사용하면
그 코드는 분기 구문으로 이해하고
결과 빌더의 메서드 중 하나가 해당 코드를 변환한다는 의미입니다.

조건 표현식의 분기 중 하나가 오류를 발생하더라도
`try` 표현식에 조건 표현식을 작성하면 안됩니다.

> Grammar of a conditional expression:
>
> *conditional-expression* → *if-expression* | *switch-expression*
>
> *if-expression* → **`if`** *condition-list* **`{`** *statement* **`}`** *if-expression-tail* \
> *if-expression-tail* → **`else`** *if-expression* \
> *if-expression-tail* → **`else`** **`{`** *statement* **`}`**
>
> *switch-expression* → **`switch`** *expression* **`{`** *switch-expression-cases* **`}`** \
> *switch-expression-cases* → *switch-expression-case* *switch-expression-cases*_?_ \
> *switch-expression-case* → *case-label* *statement* \
> *switch-expression-case* → *default-label* *statement*

### 클로저 표현식 (Closure Expression)

*클로저 표현식(closure expression)*은 클로저를 생성하는 표현식이며
다른 프로그래밍 언어에서
*람다(lambda)*나 *익명 함수(anonymous function)*라고 알고 있습니다.
함수 선언과 같이
클로저는 구문을 포함하고
둘러싸인 범위에서 상수와 변수를 캡처합니다.
형식은 다음과 같습니다:

```swift
{ (<#parameters#>) -> <#return type#> in
   <#statements#>
}
```

*파라미터(parameters)*는
<doc:Declarations#함수-선언-Function-Declaration>에서 설명했듯이
함수 선언의 파라미터 형식과 동일합니다.

클로저 표현식에 `throws`나 `async`를 명시하면
해당 클로저가 오류를 던지거나 비동기 작업을 수행하는 것을 나타냅니다.

```swift
{ (<#parameters#>) async throws -> <#return type#> in
   <#statements#>
}
```

클로저 본문에 `throws` 구문이나 `try` 표현식이 포함되어 있고
`do` 구문 내에 오류를 처리하지 않는 경우에
이 클로저는 오류를 던지는 것으로 간주됩니다.
클로저가 하나의 오류 타입만 던지면,
클로저는 해당 오류 타입을 던지는 것으로 간주됩니다;
그렇지 않으면 `any Error`를 던지는 것으로 간주됩니다.
마찬가지로 `await` 표현식이 포함되어 있다면,
비동기로 간주됩니다.

클로저를 보다 간결하게 작성할 수 있는
몇 가지 특별한 형식이 있습니다:

<!-- Apple Books screenshot ends here. -->

- 클로저는
  파라미터의 타입, 반환 타입 또는 둘 다 생략할 수 있습니다.
  파라미터 이름과 타입 모두 생략하는 경우
  구문 전에 `in` 키워드도 생략합니다.
  생략한 타입을 추론할 수 없으면
  컴파일 시 오류가 발생합니다.
- 클로저는 파라미터 이름을 생략할 수 있습니다.
  그러면 파라미터는 암시적으로
  `$` 다음에 위치가 붙어 이름이 붙여집니다:
  `$0`, `$1`, `$2`, 등.
- 단일 표현식으로만 구성된 클로저는
  해당 표현식의 값을 반환하는 것으로 간주됩니다.
  이 표현식의 내용은
  주변 표현식에 대한 타입 추론을 수행할 때도 고려됩니다.

다음의 클로저 표현식은 동일합니다:

```swift
myFunction { (x: Int, y: Int) -> Int in
    return x + y
}

myFunction { x, y in
    return x + y
}

myFunction { return $0 + $1 }

myFunction { $0 + $1 }
```

<!--
  - test: `closure-expression-forms`

  ```swifttest
  >> func myFunction(f: (Int, Int) -> Int) {}
  -> myFunction { (x: Int, y: Int) -> Int in
         return x + y
     }
  ---
  -> myFunction { x, y in
         return x + y
     }
  ---
  -> myFunction { return $0 + $1 }
  ---
  -> myFunction { $0 + $1 }
  ```
-->

함수에 인자로 클로저를 전달하는 것에 대한 정보는
<doc:Expressions#함수-호출-표현식-Function-Call-Expression>을 참고바랍니다.

클로저 표현식은 함수 호출의 일부로 클로저를 즉시 사용할 때와 같이
변수나 상수에 저장하지 않고
사용할 수 있습니다.
위 코드에서 `myFunction`에 전달된 클로저 표현식은
즉시 사용되는 예시입니다.
결과적으로
클로저 표현식은 탈출(escaping)인지 비탈출(nonescaping)인지 여부는
주변의 컨텍스트에 의해 결정됩니다.
클로저 표현식은 즉시 호출되거나
비탈출 함수 인자로 전달되면
비탈출 클로저입니다.
그렇지 않으면 클로저 표현식은 탈출 클로저입니다.

탈출 클로저에 대한 자세한 내용은 <doc:Closures#탈출-클로저-Escaping-Closures>를 참고바랍니다.

#### 캡처 목록 (Capture Lists)

기본적으로 클로저 표현식은
클로저를 감싼 범위의 상수와 변수를
강한 참조로 캡처합니다.
*캡처 목록(capture list)*을 사용하여
클로저에서 값을 어떻게 캡처할지 명시적으로 제어할 수 있습니다

캡처 목록은
대괄호 안에 콤마로 구분하여 표현식을 나열해 작성하고
파라미터 목록 앞에 작성합니다.
캡처 목록을 사용하면 파라미터 이름, 파라미터 타입, 반환 타입을 생략하더라도
`in` 키워드를 작성해야 합니다.

캡처 목록의 항목은
클로저가 생성될 때 초기화됩니다.
캡처 목록의 각 항목에 대해
상수는 외부 범위에서
이름이 같은 상수나 변수의 값으로
초기화 됩니다.
예를 들어 아래 코드에서
`a`는 캡처 목록에 포함되지만 `b`는 포함되지 않으므로
다른 동작을 보여줍니다.

```swift
var a = 0
var b = 0
let closure = { [a] in
    print(a, b)
}

a = 10
b = 10
closure()
// Prints "0 10"
```

<!--
  - test: `capture-list-value-semantics`

  ```swifttest
  -> var a = 0
  -> var b = 0
  -> let closure = { [a] in
      print(a, b)
  }
  ---
  -> a = 10
  -> b = 10
  -> closure()
  <- 0 10
  ```
-->

주변 범위의 변수와
클로저 범위의 상수로
`a`라는 이름으로 두 가지가 있지만,
`b`는 변수로 하나만 존재합니다.
내부 범위의 `a`는
클로저가 생성될 때
외부 범위의 `a` 값으로 초기화 되지만,
해당 값은 서로 연결되지 않습니다.
이것은 외부 범위의 `a`의 값이 변경되더라도
내부 범위의 `a`의 값은 영향을 받지 않고
클로저 내의 `a`가 변경되어도
클로저 외부 `a`의 값은 영향을 받지 않는다는 의미입니다.
반대로 외부 범위의 `b`는
하나의 변수로만 있으므로
클로저 내부나 외부에서 변경되면 두 위치 모두에 반영됩니다.

<!--
  [Contributor 6004] also describes the distinction as
  "capturing the variable, not the value"
  but he notes that we don't have a rigorous definition of
  capturing a variable in Swift
  (unlike some other languages)
  so that description's not likely to be very helpful for developers.
-->

이 차이는
캡처된 변수의 타입이 참조 의미(reference semantics)를 가질 경우에는 눈에 띄지 않습니다.
예를 들어
아래 코드에서 `x`라는 이름의 두 가지가 있는데
하나는 외부 범위의 변수와 또 다른 하나는 내부 범위의 상수이지만
둘 다 참조 의미이기 때문에
동일한 객체를 참조합니다.

```swift
class SimpleClass {
    var value: Int = 0
}
var x = SimpleClass()
var y = SimpleClass()
let closure = { [x] in
    print(x.value, y.value)
}

x.value = 10
y.value = 10
closure()
// Prints "10 10"
```

<!--
  - test: `capture-list-reference-semantics`

  ```swifttest
  -> class SimpleClass {
         var value: Int = 0
     }
  -> var x = SimpleClass()
  -> var y = SimpleClass()
  -> let closure = { [x] in
         print(x.value, y.value)
     }
  ---
  -> x.value = 10
  -> y.value = 10
  -> closure()
  <- 10 10
  ```
-->

<!--
  - test: `capture-list-with-commas`

  ```swifttest
  -> var x = 100
  -> var y = 7
  -> var f: () -> Int = { [x, y] in x+y }
  >> let r0 = f()
  >> assert(r0 == 107)
  ```
-->

<!--
  It's not an error to capture things that aren't included in the capture list,
  although maybe it should be.  See also rdar://17024367.
-->

<!--
  - test: `capture-list-is-not-exhaustive`

  ```swifttest
  -> var x = 100
     var y = 7
     var f: () -> Int = { [x] in x }
     var g: () -> Int = { [x] in x+y }
  ---
  -> let r0 = f()
  -> assert(r0 == 100)
  -> let r1 = g()
  -> assert(r1 == 107)
  ```
-->

표현식의 값의 타입이 클래스라면
표현식의 값을
약한 참조나 미소유 참조로 캡처하기 위해
`weak`나 `unowned`로 캡처 목록에 표현식을 표시할 수 있습니다.

```swift
myFunction { print(self.title) }                    // implicit strong capture
myFunction { [self] in print(self.title) }          // explicit strong capture
myFunction { [weak self] in print(self!.title) }    // weak capture
myFunction { [unowned self] in print(self.title) }  // unowned capture
```

<!--
  - test: `closure-expression-weak`

  ```swifttest
  >> func myFunction(f: () -> Void) { f() }
  >> class C {
  >> let title = "Title"
  >> func method() {
  -> myFunction { print(self.title) }                    // implicit strong capture
  -> myFunction { [self] in print(self.title) }          // explicit strong capture
  -> myFunction { [weak self] in print(self!.title) }    // weak capture
  -> myFunction { [unowned self] in print(self.title) }  // unowned capture
  >> } }
  >> C().method()
  << Title
  << Title
  << Title
  << Title
  ```
-->

캡처 목록의 명명 값에
임의의 표현식을 바인딩할 수도 있습니다.
표현식은 클로저가 생성될 때 평가되고,
값은 캡처됩니다.
예를 들어:

```swift
// Weak capture of "self.parent" as "parent"
myFunction { [weak parent = self.parent] in print(parent!.title) }
```

<!--
  - test: `closure-expression-capture`

  ```swifttest
  >> func myFunction(f: () -> Void) { f() }
  >> class P { let title = "Title" }
  >> class C {
  >> let parent = P()
  >> func method() {
  // Weak capture of "self.parent" as "parent"
  -> myFunction { [weak parent = self.parent] in print(parent!.title) }
  >> } }
  >> C().method()
  << Title
  ```
-->

클로저 표현식의 자세한 내용과 예시는
<doc:Closures#클로저-표현식-Closure-Expressions>을 참고바랍니다.
캡처 목록의 자세한 내용과 예시는
<doc:AutomaticReferenceCounting#클로저의-강한-순환-참조-해결-Resolving-Strong-Reference-Cycles-for-Closures>을 참고바랍니다.

<!--
  - test: `async-throwing-closure-syntax`

  ```swifttest
  >> var a = 12
  >> let c1 = { [a] in return a }                  // OK -- no async or throws
  >> let c2 = { [a] async in return a }            // ERROR
  >> let c3 = { [a] async -> in return a }         // ERROR
  >> let c4 = { [a] () async -> Int in return a }  // OK -- has () and ->
  !$ error: expected expression
  !! let c3 = { [a] async -> in return a }         // ERROR
  !! ^
  !$ error: unable to infer type of a closure parameter 'async' in the current context
  !! let c2 = { [a] async in return a }            // ERROR
  !! ^
  // NOTE: The error message for c3 gets printed by the REPL before the c2 error.
  ```
-->

> Grammar of a closure expression:
>
> *closure-expression* → **`{`** *attributes*_?_ *closure-signature*_?_ *statements*_?_ **`}`**
>
> *closure-signature* → *capture-list*_?_ *closure-parameter-clause* **`async`**_?_ *throws-clause*_?_ *function-result*_?_ **`in`** \
> *closure-signature* → *capture-list* **`in`**
>
> *closure-parameter-clause* → **`(`** **`)`** | **`(`** *closure-parameter-list* **`)`** | *identifier-list* \
> *closure-parameter-list* → *closure-parameter* | *closure-parameter* **`,`** *closure-parameter-list* \
> *closure-parameter* → *closure-parameter-name* *type-annotation*_?_ \
> *closure-parameter* → *closure-parameter-name* *type-annotation* **`...`** \
> *closure-parameter-name* → *identifier*
>
> *capture-list* → **`[`** *capture-list-items* **`]`** \
> *capture-list-items* → *capture-list-item* | *capture-list-item* **`,`** *capture-list-items* \
> *capture-list-item* → *capture-specifier*_?_ *identifier* \
> *capture-list-item* → *capture-specifier*_?_ *identifier* **`=`** *expression* \
> *capture-list-item* → *capture-specifier*_?_ *self-expression* \
> *capture-specifier* → **`weak`** | **`unowned`** | **`unowned(safe)`** | **`unowned(unsafe)`**

### 암시적 멤버 표현식 (Implicit Member Expression)

_암시적 멤버 표현식 (implicit member expression)_ 은 타입 추론이 암시된 타입을 결정할 수 있는 컨텍스트에서 열거형 케이스 또는 타입 메서드와 같은 타입의 멤버에 접근하기 위한 축약된 방법입니다. 다음과 같은 형식을 가집니다:

```swift
.<#member name#>
```

예를 들어:

```swift
var x = MyEnumeration.someValue
x = .anotherValue
```

추론된 타입이 옵셔널이면 암시적 멤버 표현식에서 옵셔널이 아닌 타입의 멤버로 사용할 수도 있습니다.

```swift
var someOptional: MyEnumeration? = .someValue
```

암시적 멤버 표현식 뒤에는 접미사 연산자 (postfix operator) 또는 <doc:Expressions#접미사-표현식-Postfix-Expressions> 에 나열된 다른 접미사 구문이 올 수 있습니다. 이것을 _연결된 암시적 멤버 표현식 (chained postfix expressions)_ 이라 합니다. 연결된 접미사 표현식이 동일한 타입을 갖는 것이 일반적이지만 유일한 요구사항은 전체 연결된 암시적 멤버 표현식이 컨텍스트에 의해 암시된 타입으로 변환될 수 있어야 한다는 것입니다. 특히, 암시적 타입이 옵셔널인 경우 옵셔널 타입이 아닌 값을 사용할 수 있고 암시적 타입이 클래스 타입인 경우 해당 서브클래스 중 하나의 값을 사용할 수 있습니다. 예를 들어:

```swift
class SomeClass {
    static var shared = SomeClass()
    static var sharedSubclass = SomeSubclass()
    var a = AnotherClass()
}
class SomeSubclass: SomeClass { }
class AnotherClass {
    static var s = SomeClass()
    func f() -> SomeClass { return AnotherClass.s }
}
let x: SomeClass = .shared.a.f()
let y: SomeClass? = .shared
let z: SomeClass = .sharedSubclass
```

위의 코드에서 `x` 의 타입은 컨텍스트가 암시하는 타입과 정확히 일치하고 `y` 의 타입은 `SomeClass` 에서 `SomeClass?` 로 변환 가능하며 `z` 의 타입은 `SomeSubclass` 에서 `SomeClass` 로 변환 가능합니다.

> Grammar of an implicit member expression:
>
> *implicit-member-expression* → **`.`** *identifier* \
> *implicit-member-expression* → **`.`** *identifier* **`.`** *postfix-expression*

### 괄호 안 표현식 (Parenthesized Expression)

_괄호 안 표현식 (parenthesized expression)_ 은 표현식을 괄호로 둘러싸인 것으로 구성됩니다. 괄호를 사용하여 표현식을 명시적으로 그룹화 하여 작업의 우선순위를 지정할 수 있습니다. 그룹화 괄호는 표현식의 타입을 변경하지 않습니다 — 예를 들어 `(1)` 의 타입은 단순히 `Int` 입니다.

> Grammar of a parenthesized expression:
>
> *parenthesized-expression* → **`(`** *expression* **`)`**

### 튜플 표현식 (Tuple Expression)

_튜플 표현식 (tuple expression)_ 은 괄호호 묶인 콤마로 구분된 표현식의 리스트로 구성됩니다. 각 표현식은 표현식 앞에 콜론 (`:`) 으로 구분된 식별자를 가질 수 있습니다. 형식은 다음과 같습니다:

```swift
(<#identifier 1#>: <#expression 1#>, <#identifier 2#>: <#expression 2#>, <#...#>)
```

튜플 표현식의 각 식별자는 튜플 표현식의 범위 내에서 고유해야 합니다. 중첩된 튜플 표현식에서 동일한 수준의 중첩한 식별자는 고유해야 합니다. 예를 들어 `(a: 10, a: 20)` 은 레이블 `a` 가 동일한 수준에서 두번 나타나므로 유효하지 않습니다. 그러나 `(a: 10, b: (a: 1, x:2))` 는 `a` 가 두번 나타나도 외부 튜플과 내부 튜플에서 나타나므로 유효합니다.

튜플 표현식은 0개의 표현식을 포함하거나 2개 이상의 표현식을 포함할 수 있습니다. 괄호 안에 단일 표현식은 괄호 안 표현식 입니다.

> Note\
> 빈 튜플 표현식과 빈 튜플 타입 모두 Swift 에서 `()` 로 작성됩니다. `Void` 는 `()` 에 대한 타입 별칭이므로 빈 튜플 타입을 작성하기위해 사용할 수 있습니다. 그러나 모든 타입 별칭과 마찬가지로 `Void` 는 항상 타입이므로 빈 튜플 표현식으로 작성하기 위해 사용할 수 없습니다.

> Grammar of a tuple expression:
>
> *tuple-expression* → **`(`** **`)`** | **`(`** *tuple-element* **`,`** *tuple-element-list* **`)`** \
> *tuple-element-list* → *tuple-element* | *tuple-element* **`,`** *tuple-element-list* \
> *tuple-element* → *expression* | *identifier* **`:`** *expression*

### 와일드카드 표현식 (Wildcard Expression)

_와일드카드 표현식 (wildcard expression)_ 은 할당 중에 값을 명시적으로 무시하는데 사용됩니다. 예를 들어 다음의 할당에서 10 은 `x` 에 할당되고 20 은 무시됩니다:

```swift
(x, _) = (10, 20)
// x is 10, and 20 is ignored
```

> Grammar of a wildcard expression:
>
> *wildcard-expression* → **`_`**

### 매크로-확장 표현식 (Macro-Expansion Expression)

_매크로-확장 표현식 (macro-expansion expression)_ 은 매크로 이름 다음에 소괄호로 매크로의 인자를 콤마로 구분되어 구성합니다. 매크로는 컴파일 때 확장됩니다. 매크로-확장 표현식은 다음의 형식을 가집니다:

```swift
<#macro name#>(<#macro argument 1#>, <#macro argument 2#>)
```

매크로-확장 표현식은 인자를 사용하지 않는 경우에
매크로의 이름뒤에 오는 소괄호를 생략합니다.

매크로-확장 표현식은 파라미터의 기본값으로 사용할 수 있습니다.

함수 또는 메서드 파라미터의 기본값으로 사용될 때,
매크로는 함수를 정의한 위치가 아닌
호출된 소스 코드 위치를 기준으로 평가됩니다.
하지만, 기본값이 매크로 뿐만 아니라
다른 코드도 포함하는 더 큰 표현식일 경우에
해당 매크로는 함수를 정의한 위치를 기준으로 평가됩니다.

```
func f(a: Int = #line, b: Int = (#line), c: Int = 100 + #line) {
    print(a, b, c)
}
f()  // Prints "4 1 101"
```

위 함수에서,
`a` 의 기본값은 단일 매크로 표현식이므로
`f(a:b:c:)` 가 호출된 소스 코드 위치에서
해당 매크로가 평가됩니다.
반면에 `b` 와 `c` 의 값은
매크로를 포함하는 표현식으로 이루어져 있으며 ---
이 매크로는
`f(a:b:c:)` 가 정의된 소스 코드 위치에서 평가됩니다.

기본값으로 매크로를 사용할 때,
다음의 요구사항을 확인하기 위해
매크로는 확장없이 타입을 검사합니다:

- 매크로의 접근 수준은
  매크로를 사용하는 함수와 같거나 덜 제한적이어야 합니다.
- 매크로는 인자를 받지 않거나,
  인자는 문자열 보간이 없는 리터럴이어야 합니다.
- 매크로의 반환 타입은 파라미터의 타입과 일치합니다.

독립형 매크로를 호출하려면 매크로 표현식을 사용합니다.
첨부된 매크로를 호출하려면,
<doc:Attributes> 에서 설명한 커스텀 속성 구문을 사용합니다.
독립형 매크로와 첨부된 매크로 모두 아래와 같이 확장합니다:

1. Swift 는 소스 코드를 분석해서 추상 구문 트리 (AST) 를 생성합니다.

2. 매크로 구현은 AST 노드를 입력으로 수신하고 해당 매크로에 필요한 변환을 수행합니다.

3. 매크로 구현을 통해 변환된 AST 노드는 본래 AST 에 추가됩니다.

각 매크로의 확장은 독립적입니다.
그러나 성능 최적화로
Swift 는 매크로를 구현하는 외부 프로세스를 시작하고
여러 매크로를 확장 하기위해 동일한 프로세스를 재사용합니다.
매크로를 구현할 때,
해당 코드는 이전에 확장된 매크로가 무엇인지 또는
현재시간과 같이 외부 상태에 의존하면 안됩니다.

여러 역할을 가지는 중첩된 매크로와 첨부된 매크로에 대해 확장 프로세스는 반복됩니다.
중첩된 매크로-확장 표현식은 외부에서 안으로 확장됩니다.
예를 들어, 아래 코드에서
`outerMacro(_:)` 가 먼저 확장되고 `innerMacro(_:)` 에 확장되지 않은 호출이
`outerMacro(_:)` 가 입력으로 받는 추상 구문 트리에 나타납니다.

```swift
#outerMacro(12, #innerMacro(34), "some text")
```

여러 역할을 가지는 첨부된 매크로는 각 역할에 대해 한 번씩 확장됩니다.
각 확장은 동일한 AST 를 입력으로 받습니다.
Swift 는 생성된 모든 AST 노드를 수집하고
AST 에서 적절한 위치에 배치하여 전체 확장을 형성합니다.

Swift 에서 매크로의 개요는 <doc:Macros> 를 참고바랍니다.

> Grammar of a macro-expansion expression:
>
> *macro-expansion-expression* → **`#`** *identifier* *generic-argument-clause*_?_ *function-call-argument-clause*_?_ *trailing-closures*_?_

### 키-경로 표현식 (Key-Path Expression)

_키-경로 표현식 (key-path expression)_ 은 타입의 프로퍼티 또는 서브스크립트를 참조합니다. 키-값 관찰 (key-value observing) 과 같은 동적 프로그래밍 작업 (dynamic programming tasks) 에서 키-경로 표현식을 사용합니다. 형식은 다음과 같습니다:

```swift
\<#type name#>.<#path#>
```

_타입 이름 (type name)_ 은 `String`, `[Int]`, 또는 `Set<Int>` 와 같은 모든 제네릭 파라미터를 포함한 구체적 타입의 이름입니다.

_경로 (path)_ 는 프로퍼티 이름, 서브스크립트, 옵셔널 체이닝 표현식, 그리고 강제 언래핑한 표현식으로 구성됩니다. 이러한 각 키-경로 요소는 순서에 상관없이 필요한 만큼 여러번 반복할 수 있습니다.

컴파일 시에 키-경로 표현식은 [`KeyPath`](https://developer.apple.com/documentation/swift/keypath) 클래스의 인스턴스에 의해 대체됩니다.

키 경로를 사용하여 값에 접근하려면 모든 타입에서 사용할 수 있는 `subscript(keyPath:)` 서브스크립트에 키 경로를 전달해야 합니다. 예를 들어:

```swift
struct SomeStructure {
    var someValue: Int
}

let s = SomeStructure(someValue: 12)
let pathToProperty = \SomeStructure.someValue

let value = s[keyPath: pathToProperty]
// value is 12
```

_타입 이름 (type name)_ 은 타입 추론이 암시된 타입으로 결정할 수 있는 컨텍스트에서는 생략될 수 있습니다. 다음 코드는 `\SomeClass.someProperty` 대신에 `\.someProperty` 를 사용합니다:

```swift
class SomeClass: NSObject {
    @objc dynamic var someProperty: Int
    init(someProperty: Int) {
        self.someProperty = someProperty
    }
}

let c = SomeClass(someProperty: 10)
c.observe(\.someProperty) { object, change in
    // ...
}
```

_경로 (path)_ 는 식별자 키 경로 (`\.self`) 를 생성하기 위해 `self` 를 참조할 수 있습니다. 식별자 키 경로 (identity key path) 는 전체 인스턴스를 참조하므로 이를 사용하여 변수에 저장된 모든 데이터를 한번에 접근하고 변경할 수 있습니다. 예를 들어:

```swift
var compoundValue = (a: 1, b: 2)
// Equivalent to compoundValue = (a: 10, b: 20)
compoundValue[keyPath: \.self] = (a: 10, b: 20)
```

_경로 (path)_ 는 프로퍼티의 값의 프로퍼티를 참조하기 위해 마침표로 구분된 여러 프로퍼티 이름이 포함될 수 있습니다. 이 코드는 `OuterStructure` 타입의 `outer` 프로퍼티의 `someValue` 프로퍼티를 접근하기 위해 키 경로 표현식 `\OuterStructure.outer.someValue` 을 사용합니다:

```swift
struct OuterStructure {
    var outer: SomeStructure
    init(someValue: Int) {
        self.outer = SomeStructure(someValue: someValue)
    }
}

let nested = OuterStructure(someValue: 24)
let nestedKeyPath = \OuterStructure.outer.someValue

let nestedValue = nested[keyPath: nestedKeyPath]
// nestedValue is 24
```

_경로 (path)_ 는 서브스크립트의 파라미터 타입이 `Hashable` 프로토콜을 준수하는 한 대괄호를 사용하여 서브스크립트를 포함할 수 있습니다. 이 예시는 배열의 두번째 요소를 접근하기 위해 키 경로로 서브스크립트를 사용합니다:

```swift
let greetings = ["hello", "hola", "bonjour", "안녕"]
let myGreeting = greetings[keyPath: \[String].[1]]
// myGreeting is 'hola'
```

서브스크립트에서 사용된 값은 명명된 값 또는 리터럴 일 수 있습니다. 값은 값 의미로 사용하여 키 경로에서 캡처됩니다. 다음 코드는 키-경로 표현식과 클로저 모두에서 변수 `index` 를 사용하여 `greetings` 배열의 세번째 요소를 접근합니다. `index` 가 수정될 때 클로저는 새로운 인덱스를 사용하는 동안 키-경로 표현식은 여전히 세번째 요소를 참조합니다.

```swift
var index = 2
let path = \[String].[index]
let fn: ([String]) -> String = { strings in strings[index] }

print(greetings[keyPath: path])
// Prints "bonjour"
print(fn(greetings))
// Prints "bonjour"

// Setting 'index' to a new value doesn't affect 'path'
index += 1
print(greetings[keyPath: path])
// Prints "bonjour"

// Because 'fn' closes over 'index', it uses the new value
print(fn(greetings))
// Prints "안녕"
```

_경로 (path)_ 는 옵셔널 체이닝과 강제 언래핑을 사용할 수 있습니다. 이 코드는 옵셔널 문자열의 프로퍼티를 접근하기 위해 키 경로에 옵셔널 체이닝을 사용합니다:

```swift
let firstGreeting: String? = greetings.first
print(firstGreeting?.count as Any)
// Prints "Optional(5)"

// Do the same thing using a key path.
let count = greetings[keyPath: \[String].first?.count]
print(count as Any)
// Prints "Optional(5)"
```

타입 내에 깊게 중첩된 값을 접근하기 위해 키 경로의 구성요소를 혼합하고 매치할 수 있습니다. 다음 코드는 구성요소를 결합한 키-경로 표현식을 사용하여 배열에 딕셔너리의 다른 값과 프로퍼티를 접근합니다.

```swift
let interestingNumbers = ["prime": [2, 3, 5, 7, 11, 13, 17],
                          "triangular": [1, 3, 6, 10, 15, 21, 28],
                          "hexagonal": [1, 6, 15, 28, 45, 66, 91]]
print(interestingNumbers[keyPath: \[String: [Int]].["prime"]] as Any)
// Prints "Optional([2, 3, 5, 7, 11, 13, 17])"
print(interestingNumbers[keyPath: \[String: [Int]].["prime"]![0]])
// Prints "2"
print(interestingNumbers[keyPath: \[String: [Int]].["hexagonal"]!.count])
// Prints "7"
print(interestingNumbers[keyPath: \[String: [Int]].["hexagonal"]!.count.bitWidth])
// Prints "64"
```

일반적으로 함수 또는 클로저를 제공하는 컨텍스트에서 키 경로 표현식을 사용할 수 있습니다. 특히, 루트 타입이 `SomeType` 이고 타입 `(SomeType) -> Value` 의 함수 또는 클로저 대신에 타입 `Value` 의 값을 생성하는 키 경로 표현식을 사용할 수 있습니다.

```swift
struct Task {
    var description: String
    var completed: Bool
}
var toDoList = [
    Task(description: "Practice ping-pong.", completed: false),
    Task(description: "Buy a pirate costume.", completed: true),
    Task(description: "Visit Boston in the Fall.", completed: false),
]

// Both approaches below are equivalent.
let descriptions = toDoList.filter(\.completed).map(\.description)
let descriptions2 = toDoList.filter { $0.completed }.map { $0.description }
```

키 경로 표현식의 문제는 표현식이 평가되는 시점에만 평가됩니다. 예를 들어 키 경로 표현식에 서브스크립트 내에 함수 호출하는 경우 함수는 키 경로가 사용될 때마다가 아니라 표현식 평가의 일부로 한번만 호출됩니다.

```swift
func makeIndex() -> Int {
    print("Made an index")
    return 0
}
// The line below calls makeIndex().
let taskKeyPath = \[Task][makeIndex()]
// Prints "Made an index"

// Using taskKeyPath doesn't call makeIndex() again.
let someTask = toDoList[keyPath: taskKeyPath]
```

Objective-C API 와 함께 상혹작용하는 코드에서 키 경로를 사용하는 것에 대한 자세한 내용은 [Swift 에서 Objective-C 런타임 특성 사용 (Using Objective-C Runtime Features in Swift)](https://developer.apple.com/documentation/swift/using_objective_c_runtime_features_in_swift) 을 참고바랍니다. 키-값 코딩과 키-값 관찰에 대한 자세한 내용은 [키-값 코딩 프로그래밍 가이드 (Key-Value Coding Programming Guide)](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html#//apple_ref/doc/uid/10000107i) 와 [키-값 관찰 프로그래밍 가이드 (Key-Value Observing Programming Guide)](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i) 을 참고바랍니다.

> Grammar of a key-path expression:
>
> *key-path-expression* → **`\`** *type*_?_ **`.`** *key-path-components* \
> *key-path-components* → *key-path-component* | *key-path-component* **`.`** *key-path-components* \
> *key-path-component* → *identifier* *key-path-postfixes*_?_ | *key-path-postfixes*
>
> *key-path-postfixes* → *key-path-postfix* *key-path-postfixes*_?_ \
> *key-path-postfix* → **`?`** | **`!`** | **`self`** | **`[`** *function-call-argument-list* **`]`**

### 선택기 표현식 (Selector Expression)

선택기 표현식 (selector expression) 을 사용하면 Objective-C 에 메서드 또는 프로퍼티의 getter 또는 setter 를 참조하는데 사용되는 선택기 (selector) 에 접근할 수 있습니다. 형식은 다음과 같습니다:

```swift
#selector(<#method name#>)
#selector(getter: <#property name#>)
#selector(setter: <#property name#>)
```

_메서드 이름 (method name)_ 과 _프로퍼티 이름 (property name)_ 은 Objective-C 런타임에 사용할 수 있는 메서드 또는 프로퍼티에 대한 참조여야 합니다. 선택기 표현식의 값은 `Selector` 타입의 인스턴스입니다. 예를 들어:

```swift
class SomeClass: NSObject {
    @objc let property: String

    @objc(doSomethingWithInt:)
    func doSomething(_ x: Int) { }

    init(property: String) {
        self.property = property
    }
}
let selectorForMethod = #selector(SomeClass.doSomething(_:))
let selectorForPropertyGetter = #selector(getter: SomeClass.property)
```

프로퍼티의 getter 에 대한 선택기를 생성할 때 _프로퍼티 이름 (property name)_ 은 변수 또는 상수 프로퍼티에 참조될 수 있습니다. 반대로 프로퍼티의 setter 에 대한 선택기를 생성할 때 _프로퍼티 이름 (property name)_ 은 변수 프로퍼티에만 참조될 수 있습니다.

_메서드 이름 (method name)_ 은 그룹화 (grouping) 을 위해 소괄호와 이름을 공유하지만 타입 서명이 다른 메서드를 명확하기 하기위해 `as` 연산자도 포함할 수 있습니다. 예를 들어:

```swift
extension SomeClass {
    @objc(doSomethingWithString:)
    func doSomething(_ x: String) { }
}
let anotherSelector = #selector(SomeClass.doSomething(_:) as (SomeClass) -> (String) -> Void)
```

선택기는 런타임이 아닌 컴파일 시 생성되기 때문에 컴파일러는 메서드 또는 프로퍼티가 존재하고 Objective-C 런타임에 노출되는지 확인할 수 있습니다.

> Note\
> _메서드 이름 (method name)_ 과 _프로퍼티 이름 (property name)_ 은 표현식이지만 절대 평가되지 않습니다.

Objective-C API 와 상호작용하는 Swift 코드에서 선택기 사용에 대한 자세한 내용은 [Swift 에서 Objective-C 런타임 특성 사용 (Using Objective-C Runtime Features in Swift)](https://developer.apple.com/documentation/swift/using_objective_c_runtime_features_in_swift) 을 참고바랍니다.

> Grammar of a selector expression:
>
> *selector-expression* → **`#selector`** **`(`** *expression* **`)`** \
> *selector-expression* → **`#selector`** **`(`** **`getter:`** *expression* **`)`** \
> *selector-expression* → **`#selector`** **`(`** **`setter:`** *expression* **`)`**

### 키-경로 문자열 표현식 (Key-Path String Expression)

키-경로 문자열 표현식 (key-path string expression) 을 사용하면 키-값 코딩 (key-value coding) 과 키-값 관찰 (key-value observing) API 에서 사용하기 위해 Objective-C 에서 프로퍼티 참조에 사용되는 문자열을 접근할 수 있습니다. 형식은 다음과 같습니다:

```swift
#keyPath(<#property name#>)
```

_프로퍼티 이름 (property name)_ 은 Objective-C 런타임에 사용할 수 있는 프로퍼티를 참조해야 합니다. 컴파일 시에 키-경로 문자열 표현식은 문자열 리터럴에 의해 대체됩니다. 예를 들어:

```swift
class SomeClass: NSObject {
    @objc var someProperty: Int
    init(someProperty: Int) {
        self.someProperty = someProperty
    }
}

let c = SomeClass(someProperty: 12)
let keyPath = #keyPath(SomeClass.someProperty)

if let value = c.value(forKey: keyPath) {
    print(value)
}
// Prints "12"
```

클래스 내에 키-경로 문자열 표현식을 사용할 때 클래스 이름 없이 프로퍼티 이름 만으로 해당 클래스의 프로퍼티에 참조할 수 있습니다.

```swift
extension SomeClass {
    func getSomeKeyPath() -> String {
        return #keyPath(someProperty)
    }
}
print(keyPath == c.getSomeKeyPath())
// Prints "true"
```

키 경로 문자열은 런타임이 아닌 컴파일 시에 생성되기 때문에 컴파일러는 프로퍼티가 존재하고 해당 프로퍼티가 Objective-C 런타임에 노출되는지 확인할 수 있습니다.

Objective-C API 와 상호작용하는 Swift 코드에서 키 경로를 사용하는 것에 대한 자세한 내용은 [Swift 에서 Objective-C 런타임 특성 사용 (Using Objective-C Runtime Features in Swift)](https://developer.apple.com/documentation/swift/using_objective_c_runtime_features_in_swift) 을 참고바랍니다. 키-값 코딩과 키-값 관찰에 대한 자세한 내용은 [키-값 코딩 프로그래밍 가이드 (Key-Value Coding Programming Guide)](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html#//apple_ref/doc/uid/10000107i) 와 [키-값 관찰 프로그래밍 가이드 (Key-Value Observing Programming Guide)](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i) 를 참고바랍니다.

> Note\
> _프로퍼티 이름 (property name)_ 은 표현식이지만 절대 평가되지 않습니다.

> Grammar of a key-path string expression:
>
> *key-path-string-expression* → **`#keyPath`** **`(`** *expression* **`)`**

## 접미사 표현식 (Postfix Expressions)

_접미사 표현식 (Postfix expressions)_ 은 접미사 연산자 (postfix operator) 또는 다른 접미사 구문 (other postfix syntax) 을 표현식에 적용하여 형성됩니다. 구문적으로 모든 기본 표현식은 접미사 표현식 입니다.

이러한 연산자의 동작에 대한 자세한 내용은 <doc:BasicOperators> 와 <doc:AdvancedOperators> 를 참고바랍니다.

Swift 표준 라이브러리에 의해 제공되는 연산자에 대한 자세한 내용은 [연산자 선언 (Operator Declarations)](https://developer.apple.com/documentation/swift/operator_declarations) 을 참고바랍니다.

> Grammar of a postfix expression:
>
> *postfix-expression* → *primary-expression* \
> *postfix-expression* → *postfix-expression* *postfix-operator* \
> *postfix-expression* → *function-call-expression* \
> *postfix-expression* → *initializer-expression* \
> *postfix-expression* → *explicit-member-expression* \
> *postfix-expression* → *postfix-self-expression* \
> *postfix-expression* → *subscript-expression* \
> *postfix-expression* → *forced-value-expression* \
> *postfix-expression* → *optional-chaining-expression*

### 함수 호출 표현식 (Function Call Expression)

_함수 호출 표현식 (function call expression)_ 은 함수 이름 다음에 소괄호로 둘러싸이고 콤마로 구분된 함수의 인자의 리스트로 구성됩니다. 함수 호출 표현식 형식은 다음과 같습니다:

```swift
<#function name#>(<#argument value 1#>, <#argument value 2#>)
```

_함수 이름 (function name)_ 은 값이 함수 타입인 모든 표현식이 될 수 있습니다.

함수 정의가 파라미터에 대한 이름을 포함하는 경우 함수 호출은 콜론 (`:`) 으로 분리하여 인자값 전에 이름을 반드시 포함해야 합니다. 이러한 종류의 함수 호출 표현식 형식은 다음과 같습니다:

```swift
<#function name#>(<#argument name 1#>: <#argument value 1#>, <#argument name 2#>: <#argument value 2#>)
```

함수 호출 표현식은 닫는 소괄호 바로 다음에 클로저 표현식의 형식으로 후행 클로저 (trailing closures) 를 포함할 수 있습니다. 이 후행 클로저는 마지막 괄호 인자 후에 추가된 함수의 인자로 이해됩니다. 첫번째 클로저 표현식은 레이블이 없습니다; 모든 추가 클로저 표현식은 인자 레이블이 앞에 옵니다. 아래 예시에서 후행 클로저 구문을 사용하거나 사용하지 않는 함수 호출의 동등한 버전을 보여줍니다:

```swift
// someFunction takes an integer and a closure as its arguments
someFunction(x: x, f: { $0 == 13 })
someFunction(x: x) { $0 == 13 }

// anotherFunction takes an integer and two closures as its arguments
anotherFunction(x: x, f: { $0 == 13 }, g: { print(99) })
anotherFunction(x: x) { $0 == 13 } g: { print(99) }
```

후행 클로저만 함수의 인자인 경우 소괄호를 생략할 수 있습니다.

```swift
// someMethod takes a closure as its only argument
myData.someMethod() { $0 == 13 }
myData.someMethod { $0 == 13 }
```

인자에 후행 클로저를 포함하려면 컴파일러는 다음과 같이 왼쪽에서 오른쪽으로 함수의 파라미터를 검사합니다:

| 후행 클로저 |     파라미터    |                                    동작                                   |
| :----: | :---------: | :---------------------------------------------------------------------: |
|   레이블   |      레이블     |             레이블이 동일하면 클로저는 파라미터와 매치됩니다; 그렇지 않으면 파라미터를 건너뜁니다.             |
|   레이블   |    레이블 없음    |                               파라미터를 건너뜁니다.                              |
|  레이블 없음 | 레이블 또는 레이블 없음 | 아래 정의된대로 파라미터가 구조적으로 함수 타입과 유사하면 클로저는 파라미터와 매치됩니다; 그렇지 않으면 파라미터를 건너뜁니다. |

후행 클로저는 매치하는 파라미터에 대해 인자로 전달됩니다. 검색 프로세스 중에 건너뛴 파라미터에는 인자가 전달되지 않습니다 — 예를 들어 기본 파라미터를 사용할 수 있습니다. 일치하는 항목을 찾은 후에 다음 후행 클로저와 다음 파라미터 검색을 계속 합니다. 일치 프로세스가 끝나면 모든 후행 클로저는 매치되어야 합니다.

파라미터가 in-out 파라미터가 아니고 파라미터가 다음 중 하나인 경우 _구조적 (structurally)_ 으로 함수 타입과 _유사합니다 (resembles)_:

* `(Bool) -> Int` 와 같이 타입이 함수 타입인 파라미터
* `@autoclosure () -> ((Bool) -> Int)` 와 같이 래핑된 표현식의 타입이 함수 타입인 autoclosure 파라미터
* `((Bool) -> Int)...` 와 같이 배열 요소 타입이 함수 타입인 가변 파라미터
* `Optional<(Bool) -> Int>` 와 같이 타입이 하나 이상의 옵셔널 레이어에 래핑된 파라미터
* `(Optional<(Bool) -> Int>)...` 와 같이 타입이 이러한 허용된 타입을 결합하는 파라미터

후행 클로저는 타입이 함수 타입과 구조적으로 유사하지만 함수는 아닌 파리미터와 일치하면 클로저는 필요에 따라 래핑됩니다. 예를 들어 파라미터의 타입이 옵셔널 타입인 경우 클로저는 자동으로 `Optional` 로 래핑됩니다.

오른쪽에서 왼쪽으로 매칭을 수행하는 Swift 5.3 이전 버전에서 코드를 쉽게 마이그레이션 하기위해 컴파일러는 왼쪽에서 오른쪽과 오른쪽에서 왼쪽으로 모두 확인합니다. 검색 방향이 다른 결과를 가져오면 오래된 오른쪽에서 왼쪽 순서가 사용되고 컴파일러는 경고를 발생시킵니다. Swift 의 향후 버전은 항상 왼쪽에서 오른쪽 순서가 사용될 것입니다.

```swift
typealias Callback = (Int) -> Int
func someFunction(firstClosure: Callback? = nil,
                  secondClosure: Callback? = nil) {
    let first = firstClosure?(10)
    let second = secondClosure?(20)
    print(first ?? "-", second ?? "-")
}

someFunction()  // Prints "- -"
someFunction { return $0 + 100 }  // Ambiguous
someFunction { return $0 } secondClosure: { return $0 }  // Prints "10 20"
```

위의 예시에서 "Ambiguous" 로 표시된 함수 호출은 "- 120" 을 출력하고 Swift 5.3 에서 컴파일러는 경고가 발생합니다. Swift 향후 버전은 "110 -" 이 출력될 것입니다.

클래스, 구조체, 또는 열거형 타입은 <doc:Declarations#특별한-이름의-메서드-Methods-with-Special-Names> 에서 설명한대로 여러 메서드 중 하나로 선언하여 함수 호출 구문에 대한 문법적 설탕 (syntactic sugar) 을 활성화 할 수 있습니다.

#### **포인터 타입으로 암시적 변환 (Implicit Conversion to a Pointer Type)**

함수 호출 표현식에서 인자와 파라미터가 다른 타입을 갖는 경우 컴파일러는 다음 리스트의 암시적 변환 중 하나를 적용하여 해당 타입을 일치시키려고 합니다:

* `inout SomeType` 은 `UnsafePointer<SomeType>` 또는 `UnsafeMutablePointer<SomeType>` 이 될 수 있습니다.
* `inout Array<SomeType>` 은 `UnsafePointer<SomeType>` 또는 `UnsafeMutablePointer<SomeType>` 이 될 수 있습니다.
* `Array<SomeType>` 은 `UnsafePointer<SomeType>` 이 될 수 있습니다.
* `String` 은 `UnsafePointer<CChar>` 가 될 수 있습니다.

다음 두가지 함수 호출은 동일합니다:

```swift
func unsafeFunction(pointer: UnsafePointer<Int>) {
    // ...
}
var myNumber = 1234

unsafeFunction(pointer: &myNumber)
withUnsafePointer(to: myNumber) { unsafeFunction(pointer: $0) }
```

이러한 암시적 변환에 의해 생성된 포인터는 함수 호출 동안에만 유효합니다. 정의되지 않은 동작을 방지하려면 함수 호출이 끝난 후에 코드가 포인터를 유지되지 않도록 해야합니다.

> Note\
> 암시적으로 배열을 안전하지 않은 포인터 (unsafe pointer) 로 변환할 때 Swift 는 필요에 따라 배열을 변환하거나 복사하여 배열의 저장소가 연속되도록 합니다. 예를 들어 저장소에 대한 API 계약을 작성하지 않은 `NSArray` 서브클래스에서 `Array` 로 연결된 배열에 이 구문을 사용할 수 있습니다. 배열의 저장소가 이미 연속됨을 보장해야 하므로 암시적 변환은 이 작업을 수행할 필요가 없는 경우 `Array` 대신 `ContiguousArray` 를 사용하십시오.

`withUnsafePointer(to:)` 와 같이 명시적 함수 대신 `&` 사용하는 것은 특히 함수가 여러 포인터 인자를 가지고 있을 때 저수준 C 함수 (low-level C functions) 를 더 읽기 쉽게 호출할 수 있습니다. 그러나 다른 Swift 코드에서 함수를 호출할 때 안전하지 않은 API 를 명시적으로 사용하는 대신 `&` 을 사용하는 것은 피해야 합니다.

> Grammar of a function call expression:
> 
> *function-call-expression* → *postfix-expression* *function-call-argument-clause* \
> *function-call-expression* → *postfix-expression* *function-call-argument-clause*_?_ *trailing-closures*
>
> *function-call-argument-clause* → **`(`** **`)`** | **`(`** *function-call-argument-list* **`)`** \
> *function-call-argument-list* → *function-call-argument* | *function-call-argument* **`,`** *function-call-argument-list* \
> *function-call-argument* → *expression* | *identifier* **`:`** *expression* \
> *function-call-argument* → *operator* | *identifier* **`:`** *operator*
>
> *trailing-closures* → *closure-expression* *labeled-trailing-closures*_?_ \
> *labeled-trailing-closures* → *labeled-trailing-closure* *labeled-trailing-closures*_?_ \
> *labeled-trailing-closure* → *identifier* **`:`** *closure-expression*

### 이니셜라이저 표현식 (Initializer Expression)

_이니셜라이저 표현식 (initializer expression)_ 은 타입의 이니셜라이저에 접근하는 것을 제공합니다. 형식은 다음과 같습니다:

```swift
<#expression#>.init(<#initializer arguments#>)
```

타입의 새로운 인스턴스를 초기화하기 위해 함수 호출 표현식 내에 이니셜라이저 표현식을 사용합니다. 슈퍼클래스의 이니셜라이저를 위임하기 위해 이니셜라이저 표현식을 사용할 수도 있습니다.

```swift
class SomeSubClass: SomeSuperClass {
    override init() {
        // subclass initialization goes here
        super.init()
    }
}
```

함수와 같이 이니셜라이저는 값으로 사용될 수 있습니다. 예를 들어:

```swift
// Type annotation is required because String has multiple initializers.
let initializer: (Int) -> String = String.init
let oneTwoThree = [1, 2, 3].map(initializer).reduce("", +)
print(oneTwoThree)
// Prints "123"
```

이름으로 타입을 지정하면 이니셜라이저 표현식 사용없이 타입의 이니셜라이저에 접근할 수 있습니다. 다른 모든 상황에서는 이니셜라이저 표현식을 사용해야 합니다.

```swift
let s1 = SomeType.init(data: 3)  // Valid
let s2 = SomeType(data: 1)       // Also valid

let s3 = type(of: someValue).init(data: 7)  // Valid
let s4 = type(of: someValue)(data: 5)       // Error
```

> Grammar of an initializer expression:
>
> *initializer-expression* → *postfix-expression* **`.`** **`init`** \
> *initializer-expression* → *postfix-expression* **`.`** **`init`** **`(`** *argument-names* **`)`**

### 명시적 멤버 표현식 (Explicit Member Expression)

_명시적 멤버 표현식 (explicit member expression)_ 은 명명된 타입, 튜플, 또는 모듈의 멤버에 접근을 허용합니다. 이것은 멤버의 아이템과 식별자 사이에 점 (`.`) 으로 구성됩니다.

```swift
<#expression#>.<#member name#>
```

명명된 타입의 멤버는 타입의 선언 또는 표현식의 부분으로 명명됩니다. 예를 들어:

```swift
class SomeClass {
    var someProperty = 42
}
let c = SomeClass()
let y = c.someProperty  // Member access
```

튜플의 멤버는 0을 시작으로 순서대로 정수를 사용하여 암시적으로 명명됩니다. 예를 들어:

```swift
var t = (10, 20, 30)
t.0 = t.1
// Now t is (20, 20, 30)
```

모듈의 멤버는 해당 모듈의 최상위 선언에 접근합니다.

`dynamicMemberLookup` 속성으로 선언된 타입은 <doc:Attributes> 에서 설명한대로 런타임 시 조회되는 멤버가 포함됩니다.

인자 이름만으로 이름이 다른 메서드 또는 이니셜라이저를 구별하려면 인자 이름을 소괄호 안에 포함하고 각 인자 이름 뒤에 콜론 (`:`) 을 붙입니다. 인자에 이름이 없는 경우 언더바 (`_`) 를 작성합니다. 오버로드된 메서드를 구별하려면 타입 주석을 사용합니다. 예를 들어:

```swift
class SomeClass {
    func someMethod(x: Int, y: Int) {}
    func someMethod(x: Int, z: Int) {}
    func overloadedMethod(x: Int, y: Int) {}
    func overloadedMethod(x: Int, y: Bool) {}
}
let instance = SomeClass()

let a = instance.someMethod              // Ambiguous
let b = instance.someMethod(x:y:)        // Unambiguous

let d = instance.overloadedMethod        // Ambiguous
let d = instance.overloadedMethod(x:y:)  // Still ambiguous
let d: (Int, Bool) -> Void  = instance.overloadedMethod(x:y:)  // Unambiguous
```

줄 시작에 마침표가 나타나면 암시적 멤버 표현식이 아닌 명시적 멤버 표현식의 부분으로 이해됩니다. 예를 들어 다음 리스트는 여러줄로 분할된 메서드 호출 체인을 보여줍니다:

```swift
let x = [10, 3, 20, 15, 4]
    .sorted()
    .filter { $0 > 5 }
    .map { $0 * 100 }
```

이 여러줄로 연결된 구문을 컴파일러 제어문과 결합하여 각 메서드가 호출되는 시기를 제어할 수 있습니다. 예를 들어 다음 코드는 iOS 에서 다른 필터링 규칙을 사용합니다:

```swift
let numbers = [10, 20, 33, 43, 50]
#if os(iOS)
.filter { $0 < 40 }
#else
.filter { $0 > 25 }
#endif
```

`#if`, `#endif` 와 다른 컴파일 지시문 사이에 조건부 컴파일 블록은 암시적 멤버 표현식 뒤에 접미사를 포함하지 않거나 많은 접미사를 포함하여 접미사 표현식을 형성할 수 있습니다. 다른 조건부 컴파일러 블럭 또는 이러한 표현식과 블럭을 포함할 수도 있습니다.

최상위 코드 뿐만 아니라 명시적 멤버 표현식을 작성할 수 있는 모든 곳에서 이 구문을 사용할 수 있습니다.

조건부 컴파일러 블럭에서 `#if` 컴파일러 지시문의 분기에는 하나 이상의 표현식이 포함되어야 합니다. 다른 분기는 비어있을 수 있습니다.

> Grammar of an explicit member expression:
>
> *explicit-member-expression* → *postfix-expression* **`.`** *decimal-digits* \
> *explicit-member-expression* → *postfix-expression* **`.`** *identifier* *generic-argument-clause*_?_ \
> *explicit-member-expression* → *postfix-expression* **`.`** *identifier* **`(`** *argument-names* **`)`** \
> *explicit-member-expression* → *postfix-expression* *conditional-compilation-block*
>
> *argument-names* → *argument-name* *argument-names*_?_ \
> *argument-name* → *identifier* **`:`**

### 접미사 Self 표현식 (Postfix Self Expression)

접미사 `self` 표현식 (postfix `self` expression) 은 표현식 또는 타입의 이름 뒤에 `.self` 로 구성됩니다. 형식은 다음과 같습니다:

```swift
<#expression#>.self
<#type#>.self
```

첫번째 형식은 _표현식 (expression)_ 의 값으로 평가됩니다. 예를 들어 `x.self` 는 `x` 로 평가됩니다.

두번째 형식은 _타입 (type)_ 의 값으로 평가됩니다. 값으로 타입을 접근하려면 이 형식을 사용합니다. 예를 들어 `SomeClass.self` 는 `SomeClass` 타입 자체로 평가되므로 타입 수준 인자 (type-level argument) 를 허용하는 함수나 메서드에 전달할 수 있습니다.

> Grammar of a postfix self expression:
>
> *postfix-self-expression* → *postfix-expression* **`.`** **`self`**

### 서브스크립트 표현식 (Subscript Expression)

_서브스크립트 표현식 (subscript expression)_ 은 해당 서브스크립트 선언의 getter 와 setter 를 사용하여 서브스크립트에 접근을 제공합니다. 형식은 다음과 같습니다:

```swift
<#expression#>[<#index expressions#>]
```

서브스크립트 표현식의 값을 평가하기 위해 _표현식 (expression)_ 의 타입에 대한 서브스크립트 getter 는 서브스크립트 파라미터로 _인덱스 표현식 (index expressions)_ 을 전달하여 호출됩니다. 값을 설정하기 위해선 서브스크립트 setter 는 동일한 방식으로 호출됩니다.

서브스크립트 선언에 대한 자세한 내용은 <doc:Declarations#프로토콜-서브스크립트-선언-Protocol-Subscript-Declaration> 을 참고바랍니다.

> Grammar of a subscript expression:
>
> *subscript-expression* → *postfix-expression* **`[`** *function-call-argument-list* **`]`**

### 강제-값 표현식 (Forced-Value Expression)

_강제-값 표현식 (forced-value expression)_ 은 `nil` 이 아니라고 확신하는 옵셔널 값을 언래핑 합니다. 형식은 다음과 같습니다:

```swift
<#expression#>!
```

_표현식 (expression)_ 의 값이 `nil` 이 아니라면 옵셔널 값은 언래핑 되고 해당 옵셔널이 아닌 타입을 반환합니다. 그렇지 않으면 런타임 오류가 발생합니다.

강제-값 표현식의 언래핑된 값은 값 자체를 변경하거나 값의 멤버중 하나에 할당하여 수정할 수 있습니다. 예를 들어:

```swift
var x: Int? = 0
x! += 1
// x is now 1

var someDictionary = ["a": [1, 2, 3], "b": [10, 20]]
someDictionary["a"]![0] = 100
// someDictionary is now ["a": [100, 2, 3], "b": [10, 20]]
```

> Grammar of a forced-value expression:
>
> *forced-value-expression* → *postfix-expression* **`!`**

### 옵셔널-체이닝 표현식 (Optional-Chaining Expression)

_옵셔널-체이닝 표현식 (optional-chaining expression)_ 은 접미사 표현식으로 옵셔널 값을 사용하기 위해 간단한 구문을 제공합니다. 형식은 다음과 같습니다:

```swift
<#expression#>?
```

접미사 `?` 연산자는 표현식의 값 변경없이 표현식에서 옵셔널-체이닝 표현식을 만듭니다.

옵셔널-체이닝 표현식은 접미사 표현식 내에 나타나야 하고 접미사 표현식이 특별한 방법으로 평가되도록 합니다. 옵셔널-체이닝 표현식의 값이 `nil` 이면 접미사 표현식의 다른 모든 동작은 무시되고 전체 접미사 표현식은 `nil` 로 평가됩니다. 옵셔널-체이닝 표현식의 값이 `nil` 이 아니면 옵셔널-체이닝 표현식의 값은 언래핑되고 나머지 접미사 표현식에 평가하기 위해 사용됩니다. 두 경우 모두 접미사 표현식의 값은 여전히 옵셔널 타입입니다.

옵셔널-체이닝 표현식이 포함된 접미사 표현식이 다른 접미사 표현식 내에 중첩되었다면 가장 바깥쪽 표현식만 옵셔널 타입을 반환합니다. 아래의 예시에서 `c` 가 `nil` 이 아닐 때 `.performAction()` 을 평가하기 위해 사용되는 `.property` 를 평가하는데 사용됩니다. 전체 표현식 `c?.property.performAction()` 은 옵셔널 타입의 값을 가집니다.

```swift
var c: SomeClass?
var result: Bool? = c?.property.performAction()
```

다음의 예시는 옵셔널 체이닝 사용없이 위의 예시의 동작을 보여줍니다.

```swift
var result: Bool?
if let unwrappedC = c {
    result = unwrappedC.property.performAction()
}
```

옵셔널-체이닝 표현식의 언래핑된 값은 값 자체를 변경하거나 값의 멤버중 하나에 할당을 통해 변경될 수 있습니다. 옵셔널-체이닝 표현식의 값이 `nil` 이라면 할당 연산자의 오른편의 표현식은 평가되지 않습니다. 예를 들어:

```swift
func someFunctionWithSideEffects() -> Int {
    return 42  // No actual side effects.
}
var someDictionary = ["a": [1, 2, 3], "b": [10, 20]]

someDictionary["not here"]?[0] = someFunctionWithSideEffects()
// someFunctionWithSideEffects isn't evaluated
// someDictionary is still ["a": [1, 2, 3], "b": [10, 20]]

someDictionary["a"]?[0] = someFunctionWithSideEffects()
// someFunctionWithSideEffects is evaluated and returns 42
// someDictionary is now ["a": [42, 2, 3], "b": [10, 20]]
```

> Grammar of an optional-chaining expression:
>
> *optional-chaining-expression* → *postfix-expression* **`?`**
