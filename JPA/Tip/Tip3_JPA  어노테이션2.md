











## @IdClass
- **정의**: 복합 키를 정의하는 방법 중 하나로, 엔티티 클래스 외부에 별도의 클래스(`IdClass`)를 만들어 해당 클래스에서 복합 키를 정의합니다.
- **특징**: 외부에 키 클래스를 정의하고, 그 키 클래스는 엔티티의 복합 키를 구성하는 필드를 포함합니다.
- **동작**: `@IdClass`는 해당 엔티티에서 복합 키를 사용하고, 각 필드를 `@Id`로 정의하여 외부의 키 클래스와 연결합니다.

| 속성             | 기능                                      | 기본값 |
| ---------------- | -----------------------------------------| ------ |
| **value**        | 복합 키 클래스를 명시합니다.               | 없음   |

**예제코드**
```java
@Entity
@IdClass(OrderId.class)
public class Order {

    @Id
    private Long orderId;

    @Id
    private Long customerId;

    private String orderDetails;

    // Getter, Setter ...
}

public class OrderId implements Serializable {
    private Long orderId;
    private Long customerId;

    // hashCode, equals, Getter, Setter ...
}
```

- `@IdClass(OrderId.class)`: `OrderId` 클래스를 복합 키로 사용합니다.
- `@Id`: 복합 키의 구성 요소로 사용되는 필드입니다.
- `OrderId`: `Serializable`을 구현하고, 복합 키를 정의하는 클래스입니다.

---

## @EmbeddedId
- **정의**: 복합 키를 정의하는 또 다른 방법으로, 복합 키를 내부적으로 포함하는 클래스를 엔티티에 포함시킵니다.
- **특징**: 엔티티 내에 복합 키 클래스를 포함하며, 키 클래스를 `@EmbeddedId`로 정의하여 엔티티의 기본 키로 사용합니다.
- **동작**: `@EmbeddedId`는 복합 키 클래스를 포함하는 필드를 엔티티 내에 정의하며, 이 클래스가 복합 키의 역할을 합니다.

| 속성         | 기능                     | 기본값 |
| ------------ | ------------------------ | ------ |
| **name**     | 사용할 임베디드 필드 이름 | 없음   |

**예제코드**
```java
@Entity
public class Order {

    @EmbeddedId
    private OrderId orderId;

    private String orderDetails;

    // Getter, Setter ...
}

@Embeddable
public class OrderId implements Serializable {
    private Long orderId;
    private Long customerId;

    // hashCode, equals, Getter, Setter ...
}
```

- `@EmbeddedId`: `OrderId` 클래스를 엔티티의 기본 키로 사용합니다.
- `@Embeddable`: `OrderId` 클래스를 다른 엔티티에 포함할 수 있도록 정의합니다.
- `OrderId`: 복합 키를 정의하는 클래스입니다.

---

## @MappedSuperclass
- **정의**: 공통된 속성이나 매핑 정보를 상속받을 수 있도록 하는 클래스에 사용하는 애노테이션입니다. 해당 클래스를 상속받는 엔티티는 그 클래스의 속성을 물려받지만, `@MappedSuperclass` 자체는 엔티티로 취급되지 않습니다.
- **특징**: 데이터베이스 테이블로 매핑되지 않으며, 상속받는 클래스에서만 사용됩니다.
- **동작**: 공통된 필드나 매핑 정보를 여러 엔티티 클래스에서 재사용할 수 있습니다.

| 속성         | 기능         | 기본값 |
| ------------ | ------------ | ------ |
| **없음**     | 없음         | 없음   |

**예제코드**
```java
@MappedSuperclass
public abstract class BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private LocalDateTime createdAt;

    private LocalDateTime updatedAt;

    // Getter, Setter ...
}

@Entity
public class Order extends BaseEntity {

    private String orderDetails;

    // Getter, Setter ...
}
```

- `@MappedSuperclass`: `BaseEntity` 클래스는 테이블로 매핑되지 않지만, 상속받는 엔티티들이 이 클래스의 속성을 물려받습니다.
- `BaseEntity`: 공통된 필드(`id`, `createdAt`, `updatedAt`)를 정의한 상위 클래스입니다.
- `Order`: `BaseEntity`를 상속받아 공통 필드를 재사용하는 엔티티입니다.

---

## @CollectionTable
- **정의**: 엔티티에서 `@ElementCollection`과 함께 사용되며, 컬렉션 타입의 데이터를 별도의 테이블에 매핑할 때 사용하는 애노테이션입니다. 주로 값 타입 컬렉션(예: `List`, `Set`)을 매핑할 때 사용됩니다.
- **특징**: 기본 테이블이 아닌 별도의 테이블에 값을 저장하고, 이 테이블과 엔티티 간의 연관 관계를 정의합니다.
- **동작**: 컬렉션의 각 요소는 `@CollectionTable`로 정의된 테이블에 저장되며, 이 테이블은 엔티티의 기본 키를 외래 키로 사용하여 컬렉션 요소와 연관을 만듭니다.

| 속성                | 기능                                                                                          | 기본값                |
| ------------------- | --------------------------------------------------------------------------------------------- | --------------------- |
| **name**            | 컬렉션을 저장할 테이블의 이름을 설정합니다.                                                     | 엔티티 이름과 컬렉션 필드 이름을 조합하여 자동 생성 |
| **joinColumns**     | 컬렉션 테이블과 엔티티를 연결하는 외래 키 컬럼을 설정합니다.                                    | 엔티티의 기본 키를 참조하는 외래 키 컬럼을 자동 생성 |
| **schema**          | 테이블이 속할 스키마를 지정합니다.                                                             | 없음                  |
| **catalog**         | 테이블이 속할 카탈로그를 지정합니다.                                                           | 없음                  |
| **uniqueConstraints** | 테이블에 대한 유니크 제약 조건을 설정합니다.                                                   | 없음                  |
| **indexes**         | 테이블에 대한 인덱스를 설정합니다.                                                             | 없음                  |

**예제코드**
```java
@Entity
public class Person {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @ElementCollection
    @CollectionTable(name = "person_addresses", joinColumns = @JoinColumn(name = "person_id"))
    @Column(name = "address")
    private List<String> addresses;

    // Getter, Setter ...
}
```

- `@ElementCollection`: `addresses` 필드가 값 타입 컬렉션임을 나타냅니다.
- `@CollectionTable(name = "person_addresses", joinColumns = @JoinColumn(name = "person_id"))`: `addresses` 컬렉션을 `person_addresses` 테이블에 저장하며, `person_id` 외래 키로 `Person` 엔티티와 연결합니다.
- `@Column(name = "address")`: 각 주소는 `person_addresses` 테이블의 `address` 컬럼에 저장됩니다.

**설명**
- `@CollectionTable`은 `@ElementCollection`과 함께 사용되어, 값 타입 컬렉션을 저장할 테이블을 정의합니다.
- `name = "person_addresses"`는 컬렉션 데이터가 저장될 테이블의 이름을 지정합니다.
- `joinColumns = @JoinColumn(name = "person_id")`는 컬렉션 테이블과 엔티티 간의 외래 키 관계를 설정하여, 엔티티와 컬렉션 데이터를 연결합니다.


## @Embeddable
- **정의**: 값 타입 클래스 또는 복합 키 클래스를 정의할 때 사용하는 애노테이션입니다. 이 클래스는 엔티티의 속성으로 포함되며, 별도의 독립적인 테이블이 아닌 엔티티 테이블에 매핑됩니다.
- **특징**: `@Embeddable`로 정의된 클래스는 다른 엔티티에 임베드되어 함께 사용될 수 있으며, 엔티티의 필드처럼 동작하지만 독립적인 생명 주기를 갖지 않습니다.
- **동작**: `@Embeddable` 클래스를 엔티티에 임베드하면, 해당 클래스의 필드들이 엔티티 테이블의 컬럼으로 매핑됩니다. `@EmbeddedId`를 통해 복합 키로도 사용 가능합니다.

| 속성  | 기능    | 기본값 |
| ----- | ------- | ------ |
| 없음  | 없음    | 없음   |

**예제코드**
```java
@Embeddable
public class Address {

    private String city;
    private String street;
    private String zipcode;

    // Getter, Setter ...
}

@Entity
public class Person {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @Embedded
    private Address address;

    // Getter, Setter ...
}
```

- `@Embeddable`: `Address` 클래스를 임베드할 수 있도록 정의합니다. `Address`는 독립된 엔티티가 아니라 `Person` 엔티티에 포함될 수 있는 값 타입입니다.
- `@Embedded`: `Person` 엔티티 내에서 `Address` 타입을 포함(임베드)합니다.
- **동작**: `Address` 클래스의 `city`, `street`, `zipcode` 필드가 `Person` 엔티티의 컬럼으로 매핑됩니다. 테이블이 따로 생성되지 않고, `Person` 테이블에 속성들이 추가됩니다.

---

## @Embedded
- **정의**: `@Embeddable`로 정의된 클래스를 엔티티에 포함시킬 때 사용하는 애노테이션입니다. 해당 클래스는 엔티티의 필드로 포함되며, 엔티티의 속성처럼 매핑됩니다.
- **특징**: `@Embedded`는 `@Embeddable` 클래스를 포함하여 테이블의 컬럼으로 매핑하며, 이를 통해 코드 재사용성과 모델링의 명확성을 높일 수 있습니다.
- **동작**: `@Embedded`를 사용하여 `@Embeddable`로 정의된 클래스를 엔티티 필드로 사용하면, 해당 클래스의 각 필드가 엔티티의 컬럼으로 매핑됩니다. `@AttributeOverrides`를 사용하면 임베드된 필드의 매핑 정보를 재정의할 수 있습니다.

| 속성                | 기능                                                                                          | 기본값 |
| ------------------- | --------------------------------------------------------------------------------------------- | ------ |
| **name**            | 임베디드 필드의 이름을 지정합니다. (필드에 기본적으로 적용될 필드 이름과 관련)                   | 없음   |

**예제코드**
```java
@Entity
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @Embedded
    private Address address;

    // Getter, Setter ...
}

@Embeddable
public class Address {

    private String city;
    private String street;
    private String zipcode;

    // Getter, Setter ...
}
```

- `@Embedded`: `Employee` 엔티티에서 `Address` 타입의 필드를 포함하여 해당 필드의 속성들이 엔티티 테이블의 컬럼으로 매핑됩니다.
- `Address`: `@Embeddable`로 정의되어 다른 엔티티에 임베드될 수 있는 값 타입 클래스입니다.
- **동작**: `Address` 클래스의 `city`, `street`, `zipcode` 필드가 `Employee` 엔티티 테이블의 컬럼으로 자동 매핑됩니다.

---

### @AttributeOverrides와 함께 사용
`@Embedded`는 `@AttributeOverrides`와 함께 사용하여 임베드된 클래스의 필드 매핑을 커스터마이징할 수 있습니다. 이 기능을 통해 같은 `@Embeddable` 클래스를 여러 엔티티에서 다른 컬럼 이름으로 매핑할 수 있습니다.

**예제코드**
```java
@Entity
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @Embedded
    @AttributeOverrides({
        @AttributeOverride(name = "city", column = @Column(name = "home_city")),
        @AttributeOverride(name = "street", column = @Column(name = "home_street")),
        @AttributeOverride(name = "zipcode", column = @Column(name = "home_zipcode"))
    })
    private Address homeAddress;

    @Embedded
    @AttributeOverrides({
        @AttributeOverride(name = "city", column = @Column(name = "work_city")),
        @AttributeOverride(name = "street", column = @Column(name = "work_street")),
        @AttributeOverride(name = "zipcode", column = @Column(name = "work_zipcode"))
    })
    private Address workAddress;

    // Getter, Setter ...
}

@Embeddable
public class Address {

    private String city;
    private String street;
    private String zipcode;

    // Getter, Setter ...
}
```

- `@AttributeOverrides`: 동일한 `@Embeddable` 클래스인 `Address`가 `homeAddress`와 `workAddress`로 각각 다른 컬럼에 매핑되도록 설정합니다.
- `@AttributeOverride(name = "city", column = @Column(name = "home_city"))`: `homeAddress`의 `city` 필드는 `home_city`로 매핑되고, `workAddress`의 `city` 필드는 `work_city`로 매핑됩니다.

### 정리
- `@Embeddable`는 값 타입 클래스나 복합 키 클래스를 정의하며, 독립적인 테이블로 매핑되지 않습니다.
- `@Embedded`는 `@Embeddable` 클래스를 엔티티에 포함시켜, 해당 클래스의 필드들을 엔티티의 컬럼으로 매핑합니다.
- `@AttributeOverrides`를 통해 동일한 `@Embeddable` 클래스를 여러 곳에서 다른 컬럼으로 매핑할 수 있습니다.