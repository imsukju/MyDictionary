엔티티 그래프(Entity Graph)는 JPA(Java Persistence API)에서 엔티티(테이블과 매핑된 자바 객체) 간의 관계를 효과적으로 관리하고, 데이터 로딩 성능을 최적화하기 위해 사용하는 기능입니다. 특히, 관련된 엔티티들을 한 번에 로딩해야 할 때 엔티티 그래프를 사용하면 필요에 따라 선택적으로 데이터를 가져올 수 있어 성능에 중요한 영향을 미칩니다.

### 1. 엔티티 그래프의 배경
JPA에서는 두 가지 주요한 페치(fetch) 전략이 있습니다:
- **Eager Fetch**: 관계된 엔티티를 즉시 가져오는 방식. 하지만 필요 없는 데이터를 가져오면서 성능에 악영향을 줄 수 있습니다.
- **Lazy Fetch**: 관계된 엔티티를 실제로 사용할 때까지 데이터를 가져오지 않는 방식. 하지만 필요할 때마다 데이터를 가져오는 과정에서 **N+1 문제**를 유발할 수 있습니다.

엔티티 그래프는 이러한 페치 전략을 유연하게 제어할 수 있는 도구로, 엔티티 간의 관계에서 어떤 엔티티를 미리 가져올지(lazy 또는 eager)를 결정하는 데 사용됩니다.

### 2. 엔티티 그래프의 종류
JPA에서 사용하는 엔티티 그래프는 두 가지로 나뉩니다.
- **이름 기반 엔티티 그래프(Named Entity Graph)**: 미리 정의해놓고 여러 곳에서 사용할 수 있는 그래프
- **동적 엔티티 그래프(Dynamic Entity Graph)**: 쿼리를 실행할 때마다 동적으로 정의해 사용하는 그래프

#### 2.1. 이름 기반 엔티티 그래프
이름 기반 엔티티 그래프는 엔티티 클래스에 미리 정의해 두고, 쿼리 시 사용할 수 있습니다. 이를 통해 엔티티 간 어떤 관계를 미리 로드할지 설정할 수 있습니다.

예시로, `@NamedEntityGraph` 어노테이션을 사용하여 엔티티 그래프를 정의할 수 있습니다:

```java
@Entity
@NamedEntityGraph(
    name = "Member.withOrders",
    attributeNodes = @NamedAttributeNode("orders")
)
public class Member {
    @Id
    private Long id;
    
    @OneToMany(mappedBy = "member", fetch = FetchType.LAZY)
    private List<Order> orders;

    // getters and setters
}
```

위 코드는 `Member` 엔티티에 대한 이름 기반 엔티티 그래프를 정의한 것입니다. `orders` 필드를 가져올 때, 연관된 주문(Order) 정보를 미리 로드하도록 설정되었습니다.

#### 2.2. 동적 엔티티 그래프
동적 엔티티 그래프는 쿼리 실행 시점에 엔티티 그래프를 동적으로 설정할 수 있습니다. 이를 통해 더 유연하게 필요한 데이터를 로딩할 수 있습니다.

다음과 같이 `EntityGraph`를 사용해 동적으로 엔티티 그래프를 설정할 수 있습니다:

```java
EntityGraph<?> entityGraph = entityManager.createEntityGraph(Member.class);
entityGraph.addAttributeNodes("orders");

Map<String, Object> properties = new HashMap<>();
properties.put("javax.persistence.fetchgraph", entityGraph);

Member member = entityManager.find(Member.class, memberId, properties);
```

이 코드는 특정 `Member` 엔티티를 조회하면서 해당 엔티티와 연관된 `orders` 엔티티만 미리 로드하는 엔티티 그래프를 동적으로 생성한 것입니다.

### 3. 엔티티 그래프의 어트리뷰트 노드
엔티티 그래프에서 중요한 개념 중 하나는 **어트리뷰트 노드(Attribute Node)**입니다. 어트리뷰트 노드는 어떤 필드나 연관된 엔티티를 로드할지를 정의하는 요소입니다.

예를 들어, 여러 연관된 엔티티 중 특정한 필드만을 로딩하고 싶다면 어트리뷰트 노드를 지정할 수 있습니다. 아래는 `Member` 엔티티와 연관된 `orders`와 `addresses` 필드를 미리 로드하는 엔티티 그래프의 예입니다:

```java
@Entity
@NamedEntityGraph(
    name = "Member.withOrdersAndAddresses",
    attributeNodes = {
        @NamedAttributeNode("orders"),
        @NamedAttributeNode("addresses")
    }
)
public class Member {
    @Id
    private Long id;
    
    @OneToMany(mappedBy = "member", fetch = FetchType.LAZY)
    private List<Order> orders;
    
    @OneToMany(mappedBy = "member", fetch = FetchType.LAZY)
    private List<Address> addresses;

    // getters and setters
}
```

이 그래프는 `Member` 엔티티를 조회할 때 `orders`와 `addresses` 필드를 모두 미리 로드하도록 설정되었습니다.

### 4. 엔티티 그래프 사용 예시
엔티티 그래프는 주로 성능 최적화를 위해 사용됩니다. 필요 없는 데이터 로딩을 피하거나, 관계된 엔티티를 미리 가져오는 등 다양한 상황에서 활용됩니다. 다음은 엔티티 그래프를 사용해 쿼리를 실행하는 예입니다.

```java
TypedQuery<Member> query = entityManager.createQuery(
    "SELECT m FROM Member m WHERE m.id = :id", Member.class
);
query.setParameter("id", 1L);
query.setHint("javax.persistence.fetchgraph", entityManager.getEntityGraph("Member.withOrders"));
Member member = query.getSingleResult();
```

위 코드는 `Member` 엔티티와 그에 연관된 `orders` 엔티티만 미리 로드하는 쿼리입니다. 이 방식으로 필요한 데이터만 효율적으로 가져올 수 있습니다.

### 5. 엔티티 그래프와 성능 최적화
엔티티 그래프는 성능 최적화를 위한 강력한 도구입니다. 다음과 같은 상황에서 유용하게 사용됩니다:
- **N+1 문제 해결**: 연관된 엔티티를 lazy 방식으로 가져올 때 발생하는 성능 문제를 줄이기 위해 한 번의 쿼리로 필요한 데이터를 모두 로드.
- **선택적 로딩**: 필요한 엔티티만 미리 로드하여 성능 향상.
- **쿼리 단순화**: 복잡한 조인(JOIN)을 사용하지 않고도 여러 엔티티를 한 번에 로드.

이처럼 엔티티 그래프는 데이터 로딩 전략을 세밀하게 제어할 수 있는 도구로, 성능을 최적화하고 개발자가 더 나은 방식으로 데이터 액세스를 관리할 수 있도록 도와줍니다.