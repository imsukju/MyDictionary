## @JoinColumn
- **외래 키를 명시적으로 지정**: 엔티티 간의 관계에서 어떤 컬럼이 외래 키 역할을 하는지 명시적으로 정의할 수 있습니다.
- **기본 동작 재정의**: JPA는 기본적으로 자동으로 외래 키 이름을 결정하지만, `@JoinColumn`을 사용하여 외래 키 이름을 직접 설정할 수 있습니다. 이로 인해 데이터베이스 스키마와의 일관성을 유지하거나, 특정 이름 규칙을 따를 때 유용합니다.

**@JoinColumn**은 외래 키를 매핑할 때 사용됩니다. 각 속성의 기능을 자세히 설명하면 다음과 같습니다:

| 속성                                                         | 기능                                                                                                      | 기본값                                        |
| ------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | --------------------------------------------- |
| **name**                                                     | 매핑할 외래 키 컬럼의 이름을 지정합니다. 지정하지 않으면 필드명과 참조 테이블의 기본 키 컬럼명을 조합합니다.   | 필드명 + _ + 참조하는 테이블의 기본 키 컬럼명 |
| **referencedColumnName**                                     | 외래 키가 참조하는 테이블의 특정 컬럼명을 지정합니다. 기본적으로 참조하는 테이블의 기본 키 컬럼을 참조합니다.  | 참조하는 테이블의 기본 키 컬럼명              |
| **foreignKey(DDL)**                                          | 외래 키 제약조건을 직접 지정할 수 있습니다. 이 속성은 주로 DDL을 생성할 때 사용되며, 제약 조건을 커스터마이징합니다. |                                               |
| **unique**, **nullable**, **insertable**, **updatable**, **columnDefinition**, **table** | 이 속성들은 `@Column`의 속성과 동일하게 작동하며, 외래 키 컬럼의 다양한 제약사항을 설정합니다.<br>- **unique**: 해당 외래 키 컬럼이 고유해야 하는지 여부를 설정합니다.<br>- **nullable**: 외래 키 컬럼이 NULL 값을 허용하는지 설정합니다.<br>- **insertable**: 데이터 삽입 시 외래 키 컬럼의 값을 포함할지 여부를 설정합니다.<br>- **updatable**: 데이터 수정 시 외래 키 컬럼의 값을 수정할 수 있는지 설정합니다.<br>- **columnDefinition**: DDL 생성 시 사용할 외래 키 컬럼의 SQL 정의를 직접 작성합니다.<br>- **table**: 외래 키가 존재하는 테이블을 명시할 수 있습니다. |                                               |

**예제코드**  
```java
@Entity
public class Member {

    @Id
    private Long id;

    private String username;

    // ManyToOne 관계에서 외래 키로 팀을 참조
    @ManyToOne
    @JoinColumn(name = "team_id", referencedColumnName = "id") // 외래 키를 "team_id"로 설정하고, 참조하는 팀의 기본 키 컬럼을 "id"로 설정
    private Team team;

    // Getter, Setter ...
}

@Entity
public class Team {

    @Id
    private Long id;

    private String name;

    // Getter, Setter ...
}
```

- `@JoinColumn(name = "team_id")`: `Member` 엔티티에서 `Team` 엔티티를 참조하는 외래 키 컬럼의 이름을 **team_id**로 지정합니다.
- `referencedColumnName`: 참조하는 엔티티의 기본 키를 명시하며, **id** 필드를 참조합니다.
---   
   
   
   
   
## @ManyToOne
- **정의**: 다대일 관계를 나타냅니다. 즉, 여러 엔티티가 하나의 엔티티에 연결될 때 사용됩니다.
- **특징**: 자식 엔티티(즉, N 측)에서 부모 엔티티(즉, 1 측)를 참조합니다. 외래 키는 `Many` 쪽에 존재하며, 그 외래 키를 통해 `One` 쪽 엔티티를 참조하게 됩니다.
- **동작**: 일반적으로 `Many` 측에서 데이터베이스에 외래 키를 생성하여 `One` 측의 기본 키를 참조합니다. 이는 SQL 관계형 데이터베이스에서 다대일 관계를 반영합니다.

| 속성         | 기능                                                                                                        | 기본값                                                    |
| ------------ | ----------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| **optional** | `false`로 설정하면 해당 연관 엔티티가 항상 존재해야 합니다. 즉, 외래 키 값이 NULL일 수 없습니다.             | true                                                      |
| **fetch**    | 연관된 엔티티를 로딩하는 방식(페치 전략)을 정의합니다.<br>- **FetchType.EAGER**: 즉시 로딩, 엔티티 조회 시 연관된 엔티티도 즉시 함께 조회됩니다.<br>- **FetchType.LAZY**: 지연 로딩, 연관된 엔티티를 실제로 사용할 때 로딩됩니다. | @ManyToOne=FetchType.EAGER<br>@OneToMany=FetchType.LAZY   |
| **cascade**  | 부모 엔티티의 상태 변화(예: 저장, 삭제)가 자식 엔티티에게 전이되도록 설정합니다.<br>주로 연관된 엔티티를 함께 관리해야 할 때 사용합니다. |                                                           |
| **targetEntity** | 연관된 엔티티의 타입을 명시적으로 설정할 수 있습니다. 일반적으로는 제네릭 타입을 통해 자동으로 유추되기 때문에 거의 사용되지 않습니다. |                                                           |

**예제코드**
```java
@Entity
public class Order {

    @Id
    private Long id;

    private String orderDetails;

    // ManyToOne 관계에서 고객을 참조
    @ManyToOne(fetch = FetchType.LAZY, optional = false, cascade = CascadeType.ALL) // 고객과의 관계는 Lazy 로딩, optional은 false, CascadeType.ALL 설정
    @JoinColumn(name = "customer_id") // 고객의 외래 키 컬럼을 customer_id로 설정
    private Customer customer;

    // Getter, Setter ...
}

@Entity
public class Customer {

    @Id
    private Long id;

    private String name;

    // Getter, Setter ...
}
```

- `@ManyToOne`: **Order** 엔티티는 **Customer**와 다대일 관계를 가집니다.
- `fetch = FetchType.LAZY`: 주문이 조회될 때 고객 정보는 나중에 로딩됩니다 (지연 로딩).
- `optional = false`: 주문은 항상 고객을 가지고 있어야 하며, 고객이 없을 수 없습니다.
- `cascade = CascadeType.ALL`: 주문 저장/삭제 시 관련된 고객도 함께 저장/삭제됩니다.
---   
   
   
   
   
## @OneToMany
- **정의**: 일대다 관계를 나타냅니다. 즉, 하나의 엔티티가 여러 엔티티와 연결될 때 사용됩니다.
- **특징**: 부모 엔티티(즉, 1 측)에서 자식 엔티티(즉, N 측)를 참조합니다. 외래 키는 `Many` 측에 존재하지만, 매핑은 `One` 측에서 이루어집니다. `One` 측은 종종 자식 엔티티들의 컬렉션을 포함합니다.
- **동작**: 기본적으로 `@OneToMany`는 단방향일 경우 외래 키에 대한 제어권이 없으며, 양방향 관계에서만 효과적으로 외래 키를 관리할 수 있습니다. 이를 위해 `mappedBy` 속성을 사용하여 자식 엔티티의 어떤 필드가 외래 키 관계를 관리하는지 지정해야 합니다.


| 속성         | 기능                                                                                                           | 기본값                                        |
| ------------ | -------------------------------------------------------------------------------------------------------------- | --------------------------------------------- |
| **mappedBy** | 일대다 관계에서 "다"쪽 엔티티에 있는 필드명을 지정합니다. **mappedBy**는 반대쪽 엔티티에서 매핑된 필드명을 사용하여 양방향 관계를 설정할 때 사용됩니다.<br>이 속성이 없는 경우, 기본적으로 새로운 조인 테이블이 생성됩니다. | 필수 설정값 아님                               |
| **fetch**    | 페치 전략을 설정합니다.<br>- **FetchType.LAZY**: 기본값으로, 연관된 엔티티를 실제로 사용할 때 로딩합니다.<br>- **FetchType.EAGER**: 엔티티 조회 시 연관된 엔티티를 즉시 함께 조회합니다. | FetchType.LAZY                                |
| **cascade**  | 부모 엔티티의 상태 변화가 자식 엔티티에게 전이되도록 설정합니다. 예를 들어, 부모 엔티티를 삭제하면 연관된 자식 엔티티도 함께 삭제됩니다. |                                               |
| **orphanRemoval** | **true**로 설정하면, 연관된 엔티티가 부모 엔티티와의 관계가 끊어지면 해당 엔티티를 자동으로 삭제합니다. 이를 통해 일대다 관계에서 "고아 객체"를 제거할 수 있습니다. | false                                         |

이렇게 세 가지 어노테이션(@JoinColumn, @ManyToOne, @OneToMany)의 주요 속성들을 자세히 설명하고 표로 정리하였습니다. 이를 통해 각 어노테이션의 설정과 활용 방식을 이해하는 데 도움이 될 것입니다.

**예제코드**
```java
@Entity
public class Team {

    @Id
    private Long id;

    private String name;

    // OneToMany 관계에서 Member 엔티티를 참조
    @OneToMany(mappedBy = "team", fetch = FetchType.LAZY, cascade = CascadeType.ALL, orphanRemoval = true) 
    private List<Member> members = new ArrayList<>(); // 팀과 연관된 모든 회원을 관리

    // Getter, Setter ...
}

@Entity
public class Member {

    @Id
    private Long id;

    private String username;

    @ManyToOne
    @JoinColumn(name = "team_id") // Member 엔티티가 Team 엔티티와 다대일 관계를 가짐
    private Team team;

    // Getter, Setter ...
}
```

- `@OneToMany(mappedBy = "team")`: **Team** 엔티티는 **Member** 엔티티와 일대다 관계를 가지며, **Member** 엔티티의 `team` 필드에 의해 매핑됩니다.
- `fetch = FetchType.LAZY`: **Team**이 조회될 때 **Member** 리스트는 지연 로딩됩니다.
- `cascade = CascadeType.ALL`: 팀과 관련된 모든 회원도 함께 저장/삭제됩니다.
- `orphanRemoval = true`: **Member**가 **Team**과의 관계가 끊기면 자동으로 삭제됩니다.

## @ManyToMany
- **정의**: 다대다 관계를 나타냅니다. 즉, 두 개의 엔티티가 서로 여러 개의 엔티티와 연결될 때 사용됩니다. 예를 들어, 학생(Student)과 수업(Course)이 다대다 관계를 가질 수 있으며, 하나의 학생이 여러 수업을 들을 수 있고, 하나의 수업에 여러 학생이 참여할 수 있습니다.
- **특징**: 다대다 관계에서는 관계를 관리하기 위해 중간 테이블(Join Table)이 필요합니다. 두 엔티티는 서로 상대방을 참조하며, 양방향으로 매핑될 수 있습니다. 보통 `@JoinTable` 어노테이션을 사용하여 중간 테이블의 이름과 외래 키를 정의합니다.
- **동작**: 다대다 관계는 보통 양방향으로 설정되며, 관계를 관리하는 측(owning side)에서 `mappedBy` 속성을 사용하여 반대쪽 엔티티와의 관계를 지정할 수 있습니다. 반대로 관계를 관리받는 측(inverse side)은 외래 키를 직접 관리하지 않으며, 중간 테이블을 통해 관계가 연결됩니다.

### 속성 설명

| 속성             | 기능                                                                                                           | 기본값                                     |
| ---------------- | -------------------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| **mappedBy**     | 다대다 관계에서 반대쪽 엔티티에서 매핑된 필드명을 지정합니다. 이를 통해 양방향 관계에서 관계의 관리받는 측을 설정할 수 있습니다. `mappedBy`가 없는 경우, 기본적으로 중간 테이블이 생성됩니다. | 필수 설정값 아님                           |
| **fetch**        | 페치 전략을 설정합니다.<br>- **FetchType.LAZY**: 기본값으로, 연관된 엔티티를 실제로 사용할 때 로딩합니다.<br>- **FetchType.EAGER**: 엔티티 조회 시 연관된 엔티티를 즉시 함께 조회합니다. | FetchType.LAZY                             |
| **cascade**      | 부모 엔티티의 상태 변화가 연관된 엔티티에 전이되도록 설정합니다. 예를 들어, 부모 엔티티가 저장되거나 삭제될 때 관련된 자식 엔티티에도 적용됩니다. |                                              |
| **targetEntity** | 관계가 설정된 대상 엔티티 클래스를 명시적으로 지정합니다. 주로 제네릭 타입을 사용할 때, 이 속성을 통해 매핑할 구체적인 엔티티 클래스를 명확히 지정할 수 있습니다. | 자동으로 추론                              |
| **joinTable**    | 중간 테이블의 이름과 관계된 외래 키를 정의합니다. `@JoinTable` 어노테이션을 사용하여 중간 테이블의 이름과 열 이름을 지정할 수 있습니다. 이를 통해 두 엔티티 간의 관계를 나타내는 테이블을 커스터마이징할 수 있습니다. | 자동 생성된 중간 테이블 사용               |

### @JoinTable 속성 설명

| 속성                 | 기능                                                                                              | 기본값                                     |
| -------------------- | ------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| **name**             | 중간 테이블의 이름을 정의합니다. 기본적으로는 두 엔티티 이름을 조합하여 자동으로 테이블 이름이 생성됩니다. | 자동 생성된 이름                            |
| **joinColumns**      | 현재 엔티티와 중간 테이블 간의 외래 키를 정의합니다. `@JoinColumn` 어노테이션을 사용하여 명시합니다. | 자동 생성된 외래 키 열                      |
| **inverseJoinColumns** | 반대쪽 엔티티와 중간 테이블 간의 외래 키를 정의합니다. `@JoinColumn` 어노테이션을 사용하여 명시합니다. | 자동 생성된 외래 키 열                      |

### 예시 코드

```java
@Entity
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @ManyToMany
    @JoinTable(
        name = "student_course", // 중간 테이블 이름
        joinColumns = @JoinColumn(name = "student_id"), // 현재 엔티티(Student)의 외래 키 열
        inverseJoinColumns = @JoinColumn(name = "course_id") // 반대쪽 엔티티(Course)의 외래 키 열
    )
    private List<Course> courses = new ArrayList<>();
}

@Entity
public class Course {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;

    @ManyToMany(mappedBy = "courses") // Student 엔티티에서의 관계를 역참조
    private List<Student> students = new ArrayList<>();
}
```

### 요약
`@ManyToMany`는 두 엔티티 간에 다대다 관계를 설정할 때 사용되며, 중간 테이블을 통해 관계가 관리됩니다. `mappedBy` 속성을 사용하여 양방향 관계를 설정하고, `@JoinTable`을 통해 중간 테이블과 외래 키를 커스터마이징할 수 있습니다.