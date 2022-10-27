---
title: "Kotlin에서 JPA Entity 멤버변수 private set 사용 시 주의사항"
description:
date: 2022-10-26 17:00:00
tags:
- kotlin
- spring data
- JPA
- spring
---

## 문제상황

아래 코드는 “Private setters are not allowed for open properties” 컴파일 오류가 발생한다.

```kotlin
@Entity
class Product(
    @Id @Column(columnDefinition = "BINARY(16)") val id: UUID,
    @Embedded val name: DisplayedName,
    price: Price = Price.ZERO
) {
    @Embedded
    var price: Price = price
        private set  // private 컴파일오류!

    constructor(name: DisplayedName, price: Long) : this(IdGenerator.createId(), name, Price(price))

    fun changePrice(price: Price) {
        this.price = price
    }
}

```

## 원인

JPA를 사용하려면 allopen 플러그인을 사용해야하는데, allopen 플러그인으로 인해 모든 속성 및 메서드가 스프링 구성 요소에서 오픈된다.

그래서 오픈된 속성은 private setter를 만들 수 없다.

## 해결책

JPA를 사용하면 기본생성자도 private으로 선언할 수 없는데 수정자도 마찬가지다
private → protected 로 변경하면 컴파일 오류는 발생하지 않는다.

```kotlin
@Entity
class Product(
    @Id @Column(columnDefinition = "BINARY(16)") val id: UUID,
    @Embedded val name: DisplayedName,
    price: Price = Price.ZERO
) {
    @Embedded
    var price: Price = price
        protected set

    constructor(name: DisplayedName, price: Long) : this(IdGenerator.createId(), name, Price(price))

    fun changePrice(price: Price) {
        this.price = price
    }
}
```

## 참고

- [https://stackoverflow.com/questions/45153998/what-is-an-open-property-why-i-cant-make-its-setter-to-private](https://stackoverflow.com/questions/45153998/what-is-an-open-property-why-i-cant-make-its-setter-to-private)