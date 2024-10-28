Spring Data JPA에서 **프로젝션(Projection)**은 대규모 데이터를 처리할 때 전체 엔티티가 아닌 필요한 특정 속성만을 조회할 수 있는 유용한 기능입니다. 이를 통해 쿼리 성능을 최적화하고 불필요한 데이터를 가져오는 것을 방지할 수 있습니다. 이번 설명에서는 Spring Data JPA에서 제공하는 여러 프로젝션 방법을 예시와 함께 설명하여 이해를 돕겠습니다.

### 1. 기본 개념: 프로젝션이란?
Spring Data JPA의 기본적인 쿼리 메서드는 Repository가 관리하는 엔티티, 즉 [**Aggregate Root**](./Aggregate%20Root.md) 전체 객체를 반환합니다. 하지만 모든 속성이 필요한 것이 아니라 특정 속성만 필요할 때, **프로젝션**을 사용하여 필요한 속성만 조회할 수 있습니다. 이는 특히 대규모 데이터를 다룰 때 매우 유용한 방법입니다.

### 2. 기본 예시: 엔티티와 Repository

다음은 기본적인 `Person` 엔티티와 해당 Repository의 예시입니다.

```java
class Person {
    @Id UUID id;
    String firstname, lastname;
    Address address;

    static class Address {
        String zipCode, city, street;
    }
}

interface PersonRepository extends Repository<Person, UUID> {
    Collection<Person> findByLastname(String lastname);
}
```

이 예시에서 `findByLastname` 메서드는 `Person` 객체 리스트를 반환합니다. 하지만 이름과 성만 필요하다면 전체 `Person` 엔티티를 조회하는 대신 필요한 속성만 조회하는 방식으로 최적화할 수 있습니다.

### 3. 인터페이스 기반 프로젝션(Interface-based Projections)

**인터페이스 기반 프로젝션**은 필요한 속성을 반환하는 getter 메서드를 포함하는 인터페이스를 정의하고 이를 반환 타입으로 사용하는 방식입니다. 이는 Spring Data JPA에서 가장 쉽게 사용할 수 있는 방법 중 하나입니다.

#### 3.1 인터페이스 정의

```java
interface NamesOnly {
    String getFirstname();
    String getLastname();
}
```

#### 3.2 Repository에서 인터페이스 사용

```java
interface PersonRepository extends Repository<Person, UUID> {
    Collection<NamesOnly> findByLastname(String lastname);
}
```

이렇게 정의하면 `findByLastname()` 메서드는 `NamesOnly` 인터페이스를 통해 `Person` 객체의 이름과 성만을 조회하게 됩니다. Spring Data JPA는 실행 시에 해당 인터페이스의 프록시를 생성하고, 인터페이스의 메서드 호출을 `Person` 객체의 속성으로 매핑합니다.

### 4. 재귀적 프로젝션(Recursive Projection)

재귀적 프로젝션을 사용하면 엔티티 내부에 포함된 다른 객체의 속성도 프로젝션을 적용할 수 있습니다. 예를 들어, `Person` 엔티티의 `Address` 속성 일부도 조회하려면, `AddressSummary` 인터페이스를 정의할 수 있습니다.

#### 4.1 재귀적 프로젝션 인터페이스 정의

```java
interface PersonSummary {
    String getFirstname();
    String getLastname();
    AddressSummary getAddress();

    interface AddressSummary {
        String getCity();
    }
}
```

이제 `PersonRepository`에서 `PersonSummary`를 반환하도록 메서드를 수정합니다.

```java
interface PersonRepository extends Repository<Person, UUID> {
    Collection<PersonSummary> findByLastname(String lastname);
}
```

이를 통해 `Person` 객체에서 `Firstname`, `Lastname` 및 `Address`의 `City` 속성만을 조회할 수 있습니다.

### 5. 클로즈드 프로젝션(Closed Projections)

**클로즈드 프로젝션**은 인터페이스의 메서드가 엔티티의 실제 속성과 1:1로 매핑되는 경우를 의미합니다. 즉, 인터페이스의 모든 메서드는 엔티티의 필드와 정확히 일치해야 합니다.

```java
interface NamesOnly {
    String getFirstname();
    String getLastname();
}
```

클로즈드 프로젝션은 쿼리 최적화가 가능하여 필요한 속성만 쿼리하기 때문에 성능 향상에 도움이 됩니다.

### 6. 오픈 프로젝션(Open Projections)

**오픈 프로젝션**은 Spring Expression Language(SpEL)을 사용하여 엔티티의 속성뿐만 아니라 계산된 값을 반환할 수 있는 프로젝션입니다.

#### 6.1 SpEL을 사용한 오픈 프로젝션 예시

```java
interface NamesOnly {
    @Value("#{target.firstname + ' ' + target.lastname}")
    String getFullName();
}
```

위 예시는 `firstname`과 `lastname`을 결합해 전체 이름을 반환하는 방식입니다. 하지만 SpEL을 사용하는 경우, Spring Data JPA는 쿼리 최적화를 적용하지 못할 수 있다는 단점이 있습니다.

#### 6.2 Default Method를 사용한 오픈 프로젝션

Java 8에서 도입된 **Default Method**를 사용하면 SpEL 대신 메서드 자체에서 로직을 처리할 수 있습니다.

```java
interface NamesOnly {
    String getFirstname();
    String getLastname();

    default String getFullName() {
        return getFirstname() + " " + getLastname();
    }
}
```

이 방법은 간단한 로직을 처리하는데 매우 유용하며, Spring Data JPA의 최적화 혜택을 그대로 받을 수 있습니다.

### 7. DTO 기반 프로젝션(Class-based Projections)

DTO(Data Transfer Object)를 사용하여 프로젝션을 정의할 수도 있습니다. 이 방법은 필요한 속성만 포함하는 클래스를 정의한 후 이를 반환하는 방식입니다.

#### 7.1 DTO 클래스 정의

```java
public record NamesOnlyDTO(String firstname, String lastname) {}
```

#### 7.2 Repository에서 DTO 사용

```java
interface PersonRepository extends Repository<Person, UUID> {
    Collection<NamesOnlyDTO> findByLastname(String lastname);
}
```

DTO 기반 프로젝션은 불필요한 속성을 가져오지 않으면서 필요한 데이터만 효율적으로 전송할 수 있습니다.

### 8. 동적 프로젝션(Dynamic Projections)

**동적 프로젝션**은 실행 시점에 원하는 프로젝션 타입을 선택할 수 있는 방법입니다.

#### 8.1 동적 프로젝션을 위한 Repository 메서드 정의

```java
interface PersonRepository extends Repository<Person, UUID> {
    <T> Collection<T> findByLastname(String lastname, Class<T> type);
}
```

#### 8.2 동적 프로젝션 사용 예시

```java
void someMethod(PersonRepository personRepository) {
    Collection<Person> fullResult = personRepository.findByLastname("Matthews", Person.class);
    Collection<NamesOnly> projectionResult = personRepository.findByLastname("Matthews", NamesOnly.class);
}
```

이 방식은 호출 시점에 반환할 타입을 지정할 수 있어 더 유연하게 프로젝션을 사용할 수 있습니다.

---

**Spring Data JPA의 프로젝션** 기능은 엔티티의 전체 데이터를 조회하지 않고 필요한 데이터만을 선택적으로 조회할 수 있어 성능을 크게 향상시킬 수 있습니다. 다양한 방법(인터페이스 기반, DTO 기반, 동적 프로젝션 등)이 제공되며, 각 방법은 상황에 따라 적절하게 선택하여 사용해야 합니다.