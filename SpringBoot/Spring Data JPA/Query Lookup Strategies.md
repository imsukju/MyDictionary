### JPA 모듈에서의 두 가지 쿼리 정의 방식

JPA는 데이터베이스와 상호작용하는 쿼리를 작성할 때 두 가지 주요 방식으로 이를 정의할 수 있습니다. 첫 번째는 직접 쿼리 문자열을 작성하는 방식이고, 두 번째는 메서드 이름을 기반으로 자동으로 쿼리를 생성하는 방식입니다. 이 두 가지 방법을 적절히 활용하면, 복잡한 비즈니스 로직에 따라 다양한 데이터베이스 접근 방식이 가능해집니다.

#### 1. 파생된 쿼리(Derived Queries)
파생된 쿼리는 Spring Data JPA에서 제공하는 기능으로, 메서드 이름을 기반으로 자동으로 쿼리를 생성할 수 있습니다. 즉, 개발자가 특정 규칙에 맞게 메서드 이름을 정의하면, Spring Data JPA는 해당 메서드 이름을 분석하여 적절한 SQL 쿼리를 생성합니다.

예를 들어, `findByFirstNameStartingWith`이라는 메서드를 정의하면 JPA는 자동으로 LIKE 연산자를 사용하여 "FirstName"이 주어진 접두사로 시작하는 레코드를 검색하는 SQL 쿼리를 생성합니다. 이 기능을 통해 메서드 이름만으로도 복잡한 쿼리를 작성할 필요 없이 데이터를 조회할 수 있습니다.

**파생된 쿼리에서 자주 사용되는 술어(Predicate)**
- **IsStartingWith, StartingWith, StartsWith**: 필드가 특정 문자열로 시작하는지 확인합니다.
- **IsEndingWith, EndingWith, EndsWith**: 필드가 특정 문자열로 끝나는지를 확인합니다.
- **IsNotContaining, NotContaining, NotContains**: 필드에 특정 문자열이 포함되지 않는지를 확인합니다.
- **IsContaining, Containing, Contains**: 필드에 특정 문자열이 포함되어 있는지를 확인합니다.

Spring Data JPA는 이러한 메서드 이름에 맞춰 LIKE 구문을 자동으로 생성합니다. 예를 들어, 메서드 이름에 포함된 조건에 따라 와일드카드 문자(`%`, `_`)가 SQL 쿼리 내에서 자동으로 처리되며, 이때 쿼리 아규먼트에 포함된 와일드카드 문자는 자동으로 이스케이프 처리됩니다. 이는 검색 값에 `%`가 포함되어 있을 경우, 이를 단순한 문자로 인식하도록 하기 위한 기능입니다. 기본적으로 이스케이프 문자는 `\`이지만, 필요에 따라 `@EnableJpaRepositories`의 `escapeCharacter` 속성을 설정하여 다른 이스케이프 문자를 지정할 수 있습니다.

#### 2. 선언된 쿼리(Declared Queries)
파생된 쿼리는 매우 편리하지만, 복잡한 쿼리에서는 메서드 이름이 너무 길어지거나 파생할 수 없는 경우가 있을 수 있습니다. 이때는 `@Query` 애너테이션이나 Named Queries를 사용하여 직접 쿼리를 작성하는 방식으로 문제를 해결할 수 있습니다.

**(1) JPA Named Queries**
Named Queries는 엔티티 클래스 내에서 미리 쿼리를 정의하는 방식입니다. 미리 작성된 쿼리를 통해 메서드에서 해당 쿼리를 호출하는 구조로, 재사용이 필요한 경우에 유용합니다.

**(2) @Query 애너테이션**
`@Query` 애너테이션을 사용하면 메서드에 직접 JPQL 또는 SQL 쿼리를 정의할 수 있습니다. 복잡한 비즈니스 로직이나 메서드 이름으로 표현하기 어려운 쿼리가 필요할 때 주로 사용됩니다.

### 파생된 쿼리 예시

다음은 `User` 엔티티를 사용하여 파생된 쿼리를 작성하는 예시입니다.

**User 테이블**
```sql
CREATE TABLE User (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(255),
    last_name VARCHAR(255),
    email VARCHAR(255),
    status VARCHAR(50)
);
```

**User 엔티티 클래스**
```java
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String firstName;
    private String lastName;
    private String email;
    private String status;

    // 기본 생성자와 Getter/Setter 생략
}
```

**UserRepository 인터페이스 및 파생된 쿼리 메서드**
```java
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.List;

public interface UserRepository extends JpaRepository<User, Long> {

    // 파생된 쿼리 메서드
    List<User> findByFirstNameStartingWith(String prefix);

    List<User> findByLastNameContaining(String substring);

    List<User> findByEmailNotContaining(String substring);
}
```

### Hibernate가 생성하는 SQL 예시

- **`findByFirstNameStartingWith`**: 주어진 접두사로 시작하는 `firstName`을 가진 유저를 찾습니다.
  ```sql
  SELECT u.id, u.first_name, u.last_name, u.email, u.status 
  FROM User u 
  WHERE u.first_name LIKE ? || '%';
  ```

  이 쿼리는 전달된 접두사 값으로 시작하는 모든 `first_name`을 가진 레코드를 찾습니다. SQL에서 `%`는 "어떤 문자열이든 올 수 있다"는 의미의 와일드카드입니다. 예를 들어, `findByFirstNameStartingWith("John")`을 호출하면, `John%`로 시작하는 모든 유저를 찾게 됩니다.

- **`findByLastNameContaining`**: `lastName`에 특정 문자열이 포함된 유저를 찾습니다.
  ```sql
  SELECT u.id, u.first_name, u.last_name, u.email, u.status 
  FROM User u 
  WHERE u.last_name LIKE '%' || ? || '%';
  ```

  이 쿼리는 전달된 값이 `last_name`에 포함된 모든 레코드를 찾습니다. 예를 들어, `findByLastNameContaining("Smith")`를 호출하면, `last_name`에 "Smith"라는 문자열이 포함된 모든 레코드를 반환합니다.

- **`findByEmailNotContaining`**: `email`에 특정 문자열이 포함되지 않은 유저를 찾습니다.
  ```sql
  SELECT u.id, u.first_name, u.last_name, u.email, u.status 
  FROM User u 
  WHERE u.email NOT LIKE '%' || ? || '%';
  ```

  이 쿼리는 특정 문자열을 포함하지 않는 `email`을 가진 레코드를 검색합니다.

### 선언된 쿼리(Declared Queries) 예시

때로는 메서드 이름으로 쿼리를 자동 생성하기 어렵거나, 더 복잡한 조건이 필요한 경우 선언된 쿼리를 사용해야 합니다.

**(1) @Query 애너테이션을 사용한 선언된 쿼리 예시**
```java
import org.springframework.data.jpa.repository.Query;

public interface UserRepository extends JpaRepository<User, Long> {

    @Query("SELECT u FROM User u WHERE u.firstName = ?1 AND u.lastName = ?2")
    List<User> findByFullName(String firstName, String lastName);
}
```

이 메서드가 실행되면 다음과 같은 SQL이 생성됩니다.
```sql
SELECT u.id, u.first_name, u.last_name, u.email, u.status 
FROM User u 
WHERE u.first_name = ? AND u.last_name = ?;
```

**(2) Named Query 사용 예시**
```java
import jakarta.persistence.NamedQuery;

@Entity
@NamedQuery(name = "User.findByStatus", query = "SELECT u FROM User u WHERE u.status = ?1")
public class User {
    // 엔티티 필드 및 메서드 생략
}
```

이 경우, 미리 정의된 네임드 쿼리인 `findByStatus`를 다음과 같이 호출할 수 있습니다.
```java
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByStatus(String status);
}
```

Hibernate는 이 메서드를 호출할 때 다음과 같은 SQL 쿼리를 생성합니다.
```sql
SELECT u.id, u.first_name, u.last_name, u.email, u.status 
FROM User u 
WHERE u.status = ?;
```

### 요약

- **파생된 쿼리**는 메서드 이름을 기반으로 자동으로 SQL을 생성하여 단순한 조회 쿼리를 빠르게 구현할 수 있습니다.
- **선언된 쿼리**는 복잡한 쿼리가 필요할 때, 개발자가 직접 정의한 SQL 또는 JPQL을 통해 더 세밀하게 제어할 수 있는 방식입니다.
- Spring Data JPA와 Hibernate를 통해 이러한 기능을 활용함으로써 개발자는 SQL 작성 부담을 줄이고 비즈니스 로직에 더 집중할 수 있습니다.