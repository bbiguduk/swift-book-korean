# Document Revision History

최근 변경사항을 확인할 수 있습니다.

## Revision History

### 2024-09-19(목)

- Swift 6 기반으로 문서 업데이트.
- 오탈자 수정.

### 2024-07-02(화)

- Swift 6 Beta 기반으로 문서 업데이트.
- 엄격한 동시성 검사로 변환을 위한 정보를
  <doc:Attributes#preconcurrency> 섹션에 추가.
- 특정 타입의 에러 발생에 대한 내용을
  <doc:ErrorHandling#에러-타입-지정-Specifying-the-Error-Type> 섹션에 추가.
- <doc:AccessControl> 챕터에
  패키지-수준 접근에 대한 내용 추가.

### 2024-03-08(금)

- Swift 5.10 기반으로 문서 작업
- 오탈자 수정

### 2024-02-25(일)

- Swift 5.10 Beta 기반으로 문서 작업
- 중첩된 프로토콜에 대한 내용을
  <doc:Protocols#위임-Delegation> 섹션에 추가.
- <doc:Attributes#UIApplicationMain> 과
  <doc:Attributes#NSApplicationMain> 섹션에
  더이상 사용하지 않는 정보 추가.

### 2023-12-12(화)

- Swift 5.9.2 기반으로 문서 작업
- 작업 (task), 작업 그룹 (task group), 그리고 작업 취소 (task cancellation) 에
  대한 내용을 <doc:Concurrency> 에 추가했습니다.
- 기존 Swift Package 에 매크로 구현에 대한 내용을
  <doc:Macros> 에 추가했습니다.
- Conformance 매크로를 대신해 Extension 매크로에 대한 내용을
  <doc:Attributes#attached> 에 업데이트 했습니다.

### 2023-12-03(일)

- Swift 5.9.2 Beta 기반으로 문서 작업
- `borrowing` 과 `consuming` 수식어에 대한 정보를 
  <doc:Declarations#파라미터-수식어-Parameter-Modifiers> 섹션에 추가했습니다.
- <doc:TheBasics#상수와-변수-선언-Declaring-Constants-and-Variables> 에
  상수를 선언한 후에 값을 설정에 대한 정보를 추가했습니다.
- 백 배포 (back deployment) 에 대한 정보를
  <doc:Attributes#backDeployed> 섹션에 추가했습니다.

### 2023-09-30(토)

- Swift 5.9 기반으로 문서 작업
- <doc:TheBasics> 에 옵셔널에 대한 내용을 추가
- <doc:GuidedTour> 에 동시성 예제 추가
- <doc:Attributes#결과-변환-Result-Transformations> 섹션에
  `buildPartialBlock(first:)` 와 `buildPartialBlock(accumulated:next:)` 메서드에 대한 내용 추가
- <doc:Attributes#available> 과 <doc:Statements#조건부-컴파일-블럭-Conditional-Compilation-Block> 에서
  플랫폼 목록에 visionOS 추가

### 2023-06-19(월)

* Swift 5.9 Beta 기반으로 문서 작업
* <doc:ControlFlow> 챕터와 <doc:Expressions#조건-표현식-Conditional-Expression> 섹션에 `if` 와 `switch` 표현식에 대한 내용을 추가
* 컴파일 때 코드를 생성하는 것에 대한 <doc:Macros> 챕터 추가
* <doc:OpaqueTypes> 챕터에 박스형 프로토콜 타입 (boxed protocol type) 에 대한 내용을 추가
* `buildPartialBlock(first:)` 와 `buildPartialBlock(accumulated:next:)` 메서드에 대한 내용을 <doc:Attributes#결과-빌딩-메서드-Result-Building-Methods> 섹션에 추가
* Swift-DocC 적용한 [TSPLK (The Swift Programming Language Korean)](https://bbiguduk.github.io/swift-book-korean/documentation/tsplk/) 페이지 오픈
> GitBook 과 Swift-DocC 둘 다 운영할 계획입니다.

### 2023-04-04(화)

* Swift 5.8 기반으로 문서 작업
* 에러 처리 외의 `defer` 를 표시하는 <doc:ControlFlow#연기된-동작-Deferred-Actions> 추가
* 오탈자 수정

### 2022-09-13(화)

* Swift 5.7 정식 릴리즈에 따른 오탈자 수정

### 2022-06-29(수)

* 오탈자 수정
* Swift 5.7 기반으로 문서 작업 완료
* 행위자 (actor) 와 작업 (task) 간의 데이터 전송에 대한 내용을 <doc:Concurrency#전송-가능-타입-Sendable-Types> 섹션에 추가하고 `@Sendable` 과 `@unchecked` 속성에 대한 내용을 <doc:Attributes#Sendable> 과 <doc:Attributes#unchecked> 섹션에 추가하였습니다.
* 정규 표현식 생성에 대한 내용을 <doc:LexicalStructure#정규-표현식-리터럴-Regular-Expression-Literals> 섹션에 추가하였습니다.
* `if`-`let` 형식에 대한 내용을 <doc:TheBasics#옵셔널-바인딩-Optional-Binding> 섹선에 추가하였습니다.
* `#unavailable` 에 대한 내용을 <doc:ControlFlow#사용-가능한-API-확인-Checking-API-Availability> 섹션에 추가하였습니다.

### 2022-03-17(목)

* Swift 5.6 정식 릴리즈에 따른 오탈자 수정

### 2022-01-29(토)

* Swift 5.6 기반으로 문서 작업 완료
* 연결된 메서드 호출과 다른 접미사 표현식과 관련된 `#if` 사용에 대한 정보로 <doc:Expressions#명시적-멤버-표현식-Explicit-Member-Expression> 을 업데이트
* 이미지 파일 업데이트

### 2021-10-08(금)

* <doc:Properties> 변경 사항 수정
* <doc:Concurrency> 변경 사항 수정

### 2021-08-14(토)

* Swift 5.5 기반으로 문서 작업 완료
* <doc:Concurrency> 추가
* 오탈자 수정

### 2021-04-10(토)

* WELCOME TO SWIFT 번역 완료
* LANGUAGE REFERENCE 번역 완료

### 2021-02-20(토)

* Swift 5.4 기반으로 문서 작업 완료

### 2020-10-23(금)

* Swift 5.3 기반으로 문서 작업 완료
