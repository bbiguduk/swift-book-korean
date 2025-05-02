# The Swift Programming Language (6) (한국어)

@Metadata {
  @TechnologyRoot
}

@Options(scope: global) {
  @AutomaticSeeAlso(disabled)
  @AutomaticTitleHeading(disabled)
  @AutomaticArticleSubheading(disabled)
}

<!-- ## The Swift Programming Language -->
## Overview

[https://docs.swift.org/swift-book/](https://docs.swift.org/swift-book/) 에 있는 [The Swift Programming Language](https://docs.swift.org/swift-book/) 을 한국어로 번역한 것이며, 오역 및 오타 많을 수 있습니다. 발견 시 연락처에 있는 email로 연락해 주세요!

<!-- ### 업데이트

- <doc:RevisionHistory#2023-09-30>
- <doc:RevisionHistory#2023-06-19>
- <doc:RevisionHistory#2023-04-04>
- <doc:RevisionHistory#2022-09-13>
- <doc:RevisionHistory#2022-03-17>
- <doc:RevisionHistory#2021-08-14>
- <doc:RevisionHistory#2021-02-20>
- <doc:RevisionHistory#2020-10-23> -->

### 번역용어

| 원문                           | 번역문          |
| ---------------------------- | ------------ |
| String                       | 문자열          |
| Array                        | 배열           |
| Dictionary                   | 딕셔너리         |
| Boolean                      | 부울           |
| Optional                     | 옵셔널          |
| Switch                       | 스위치          |
| Integer                      | 정수           |
| Case                         | 케이스          |
| Collection                   | 컬렉션          |
| Function                     | 함수           |
| Return                       | 반환           |
| Type                         | 타입           |
| Parameter                    | 파라미터         |
| Argument                     | 인수           |
| Tuple                        | 튜플           |
| Closure                      | 클로저          |
| Delegate                     | 대리자          |
| Class                        | 클래스          |
| Context                      | 컨텍스트         |
| Initializer                  | 이니셜라이저       |
| Deinitializer                | 디이니셜라이저    |
| Subclass                     | 서브클래스       |
| Superclass                   | 슈퍼클래스       |
| Property                     | 프로퍼티         |
| Getter                       | Getter       |
| Setter                       | Setter       |
| Method                       | 메서드          |
| Subscript                    | 서브스크립트      |
| Raw value                    | 원시값          |
| Enumeration                  | 열거형          |
| Structure                    | 구조체          |
| Associated value             | 연관된 값        |
| Protocol                     | 프로토콜         |
| Extension                    | 확장           |
| Generic                      | 제너릭          |
| Type-safe                    | 타입-세이프       |
| Annotation                   | 명시           |
| Inference                    | 추론           |
| Backtick                     | 백틱           |
| Comment                      | 주석           |
| Asterisk                     | 애스터리스크       |
| Literal                      | 리터럴          |
| Alias                        | 별칭           |
| Optional Binding             | 옵셔널 바인딩      |
| Assertion                    | 역설           |
| Precondition                 | 전제조건         |
| Operator                     | 연산자          |
| Overflow                     | 오버플로우        |
| Terminology                  | 술어           |
| Unary                        | 단항           |
| Binary                       | 이항           |
| Ternary                      | 삼항           |
| Value Type                   | 값 타입         |
| Unicode                      | 유니코드         |
| Substring                    | 부분 문자열       |
| Set                          | 집합           |
| mutable                      | 변경 가능        |
| Control                      | 제어           |
| Statement                    | 구문           |
| Implicit                     | 암시적          |
| Nested                       | 중첩           |
| Closing Over                 | 폐쇄           |
| Trailing Closure             | 후행 클로저       |
| Escaping Closure             | 탈출 클로저       |
| Autoclosure                  | 자동 클로저       |
| Actor                        | 액터           |
| Reference Type               | 참조 타입        |
| Instance Property            | 인스턴스 프로퍼티    |
| Stored Property              | 저장된 프로퍼티     |
| Computed Property            | 계산된 프로퍼티     |
| Property Observer            | 프로퍼티 관찰자     |
| Property Wrapper             | 프로퍼티 래퍼      |
| Projected Value              | 투영된 값        |
| Type Property                | 타입 프로퍼티      |
| Instance Method              | 인스턴스 메서드     |
| Type Method                  | 타입 메서드       |
| List                         | 리스트          |
| Type Subscript               | 타입 서브스크립트   |
| Inheritance                  | 상속           |
| Overriding                   | 재정의          |
| Delegation                   | 위임           |
| Designated Initializer       | 지정된 이니셜라이저   |
| Convenience Initializer      | 편의 이니셜라이저    |
| Optional Chaining            | 옵셔널 체이닝      |
| Error Handling               | 에러 처리        |
| Concurrency                  | 동시성          |
| Asynchronous                 | 비동기          |
| Parallel                     | 병렬           |
| Structed Concurrency         | 구조화된 동시성     |
| Unstructed Concurrency       | 구조화되지 않은 동시성 |
| Type Parameter               | 타입 파라미터      |
| Opaque Type                  | 불투명한 타입      |
| Automatic Reference Counting | 자동 참조 카운팅    |
| Strong Reference Cycle       | 강한 참조 사이클    |
| Weak Reference               | 약한 참조        |
| Unowned Reference            | 미소유 참조       |
| Capture List                 | 캡처 리스트       |
| Access Control               | 접근 제어        |
| Access Level                 | 접근 수준        |
| Bitwise Operator             | 비트 연산자       |
| Result Builder               | 결과 빌더        |

## Topics

### Welcome to Swift

- <doc:AboutSwift>
- <doc:Compatibility>
- <doc:GuidedTour>

### Language Guide

- <doc:TheBasics>
- <doc:BasicOperators>
- <doc:StringsAndCharacters>
- <doc:CollectionTypes>
- <doc:ControlFlow>
- <doc:Functions>
- <doc:Closures>
- <doc:Enumerations>
- <doc:ClassesAndStructures>
- <doc:Properties>
- <doc:Methods>
- <doc:Subscripts>
- <doc:Inheritance>
- <doc:Initialization>
- <doc:Deinitialization>
- <doc:OptionalChaining>
- <doc:ErrorHandling>
- <doc:Concurrency>
- <doc:Macros>
- <doc:TypeCasting>
- <doc:NestedTypes>
- <doc:Extensions>
- <doc:Protocols>
- <doc:Generics>
- <doc:OpaqueTypes>
- <doc:AutomaticReferenceCounting>
- <doc:MemorySafety>
- <doc:AccessControl>
- <doc:AdvancedOperators>

### Language Reference

- <doc:AboutTheLanguageReference>
- <doc:LexicalStructure>
- <doc:Types>
- <doc:Expressions>
- <doc:Statements>
- <doc:Declarations>
- <doc:Attributes>
- <doc:Patterns>
- <doc:GenericParametersAndArguments>
- <doc:SummaryOfTheGrammar>

### Revision History

- <doc:RevisionHistory>

### Contact

- <doc:Contact>
