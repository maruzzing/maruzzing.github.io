---
layout: post
title: Optionals
date: 2021-05-27
comments: true
categories: [Study, swift]
tags: [Swift, Optionals]
excerpt: Swift는 타입 안전성(type-safe) 언어입니다. 즉, 변수 값들에 대한 타입이 명확해하며, 잘못된 타입이 있다면 컴파일 단에서 오류로 표시되고, 런타임에서의 타입 오류는 애플리케이션 동작이 중지(crash)시킵니다. 😱
featured-image: images/swift-optionals-main.png
---

![Swift Optionals Title](/images/swift-optionals-main.png "Swift Optionals Title")

**Swift**는 타입 안전성(**type-safe**) 언어입니다. 즉, 변수 값들에 대한 타입이 명확해하며, 잘못된 타입이 있다면 컴파일 단에서 오류로 표시되고, 런타임에서의 타입 오류는 애플리케이션 동작이 중지(crash)시킵니다. 😱

### Optional

옵셔널(Optional)은 swift가 가지고 있는 큰 특징 중 하나 인데요, 옵셔널은 **값이 있을 수도 있고, 없을 수도(nil) 있는 상태인 타입(Type)** 으로, 값을 안전하게 꺼내 쓸 수 있게 하여 런타임 오류를 피할 수 있는 방법입니다.

옵셔널은 `Int?`, `String?`, `Double?` 등과 같이 타입 어노테이션에 `?` 를 붙여 표기합니다. 아래 예시를 살펴보면 `String?` 타입으로 정의된 변수는 `String`을 가지고 있거나 아무 것도 없는(`nil`) 상태라는 것이며, 초깃값을 지정하지 않으면 기본 값은 `nil`인 것을 알 수 있습니다.

```swift
var name: String?
print(name) // nil

name = "maruzzing"
print(name) // Optional("maruzzing")
```

### Unwrapping

Optional은 하나의 Type 이므로 위의 예제 line 5에서 볼 수 있듯이 값을 할당 하더라도 그 값은 `String` 타입이 아닌 `Optional String` 타입이라고 볼 수 있습니다. 따라서 `String`의 내장 함수(Built-in method)도 사용할 수 없기 때문에 옵셔널 상태의 변수를 사용하려면 **Unwrapping**이 필요합니다.

**Unwrapping** 방법으로는 forced unwrapping, optional binding 그리고 early exit이 있습니다.

### Forced Unwrapping

옵셔널 타입 변수의 값이 `nil`이 아니라는 확신이 있다면 `!` 를 통해 강제로 Unwrapping 할 수 있습니다.

```swift
var name: String?
name = "maruzzing"
print("My name is \(name)!") // My name is maruzzing!
```

아주 간단하지만 만에하나 값이 없는 경우에는 에러가 발생하므로 `nil` 체크 없이 강제로 Unwrapping 하는 것은 위험합니다. 따라서 가급적 옵셔널 상태의 변수 조작은 `nil` 값 체크가 우선되어야 합니다.

### Optional Binding (feat. if let)

옵셔널 바인딩은 **if let (혹은 if var)** 구문을 이용하여 옵셔널 값이 nil 일 때와 아닐 때를 분기하여 처리 합니다. 그리고 **값이 있는 경우, 그 값을 Unwrapping하여 임시로 특정 상수(혹은 변수)에 보관**하여 사용합니다.

```swift
var name: String?

name = "maruzzing"

if let myName = name {
    // name가 nil이 아닌 경우 실행
    print("My name is \(myName)!") // My name is maruzzing!
} else {
    // name가 nil인 경우 실행
    print("name not provided")
}
```

여기서 주의할 점은,

1. name의 Unwrapping 값을 임시로 myName에 대입한 것일 뿐, **name은 여전히 옵셔널 상태**입니다.
2. **if let 으로 선언된 상수의 scope는 if 구문** 이므로, if 문 밖에서는 myName에 접근할 수 없습니다.

```swift
var name: String?

name = "maruzzing"

if let myName = name {
    // name가 nil이 아닌 경우 실행
    print("My name is \(name)!") // My name is Optional("maruzzing")!
    print("My name is \(myName)!") // My name is maruzzing!
} else {
    // name가 nil인 경우 실행
    print("name not provided")
}

print("My name is \(myName)!") // Error! Cannot find 'myName' in scope
```

### Early exit (feat. guard let)

Unwrapping한 상수를 전역에서 사용하고 싶은 경우에는 **guard let 구문**을 사용할 수 있습니다.

guard let 구문은 조건을 만족시키지 못하는 경우 else 문으로 분기되어 함수의 실행을 종료시키는 구조입니다. 따라서, if 문과는 다르게 else 절이 꼭 작성되어야 하며, else 구문 내에서는 `return`과 같은 exit 로직이 있어야 합니다. 특성상 함수 블록 내에서 작성되어야 합니다.

**guard let에서 Unwrapping된 상수의 스코프는 guard 구문 밖**입니다.

```swift
func introduce(name: String? = nil){
    guard let myName = name else {
        // name이 nil인 경우 실행
        print("name not provided")
        return
    }

    // name nil이 아닌 경우 실행
    print("My name is \(name)!") // My name is Optional("maruzzing")!
    print("My name is \(myName)!") // My name is maruzzing!
}

introduce(name: "maruzzing")
```
