## 문제상황

findByIdOrNull 메서드를 선언했을 때 JPA 내부 구현시 오류 발생 `No property 'null' found for type 'Product'` 예외 발생

```kotlin
interface ProductRepository {
    fun save(product: Product): Product

    fun findById(id: UUID): Optional<Product>

    fun findByIdOrNull(id: UUID): Product?
}
```

```kotlin
interface JpaProductRepository : ProductRepository, JpaRepository<Product, UUID>
```

## 원인

JpaRepository<Product, UUID>와 같이 JPA인터페이스를 상속받으면 내부적으로 JPA구현체를 반환해주는데, findByIdOrNull 메서드는 구현대상이 아닌 `CrudRepositoryExtensionsKt` 에 다음과 같이 구현 되어있기 때문에 구현체를 만들면서 findByIdOrNull 메서드를 찾지 못 하여 발생하는 오류로 보인다.

```kotlin
fun <T, ID> CrudRepository<T, ID>.findByIdOrNull(id: ID): T? = findById(id).orElse(null)
```

## 해결책

아래와 같이 확장함수 ProductRepository에 확장함수로 처리하여 해결하였다

```kotlin
fun ProductRepository.findByIdOrNull(id: UUID): Product? = findById(id).orElse(null)

interface ProductRepository {
    fun save(product: Product): Product

    fun findById(id: UUID): Optional<Product>
}
```