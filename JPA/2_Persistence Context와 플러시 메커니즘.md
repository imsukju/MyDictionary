### 플러시(Flush)

**Flush(플러시)**는 **Persistence Context**에 있는 엔티티의 변경 내용을 **데이터베이스에 반영**하는 과정입니다. **EntityManager**는 내부적으로 **Persistence Context**를 관리하면서 메모리(1차 캐시)에 있는 데이터를 데이터베이스와 동기화하는데, **flush()**는 이 동기화 작업을 수행하는 메커니즘입니다.

플러시를 수행하면 다음과 같은 일들이 발생합니다:

1. **변경 감지(Dirty Checking)**: **Persistence Context**에 있는 모든 **Persistence** 상태의 엔티티를 스냅샷과 비교하여 변경된 엔티티를 찾습니다. 변경된 엔티티는 수정 쿼리(UPDATE)를 생성하여 **쓰기 지연 SQL 저장소**에 등록합니다.

2. **쓰기 지연 SQL 저장소의 쿼리를 데이터베이스에 전송**: **Persistence Context**는 엔티티의 변경 내용을 모아두고 있다가, 플러시 시점에 **쓰기 지연 SQL 저장소**에 있는 등록(INSERT), 수정(UPDATE), 삭제(DELETE) 쿼리를 데이터베이스에 전송합니다.

플러시의 중요한 역할은 **Persistence Context**의 변경 내용을 **데이터베이스에 반영**하는 것이지, **Persistence Context**를 비우거나 초기화하는 것이 아닙니다. 플러시 후에도 **Persistence Context**는 유지되며, 변경된 데이터만 데이터베이스에 동기화됩니다.

### 플러시의 세 가지 방법

**Persistence Context**를 플러시하는 방법은 세 가지가 있습니다:

1. **em.flush() 직접 호출**:  
   개발자가 **EntityManager**의 `flush()` 메서드를 직접 호출하여 **Persistence Context**의 내용을 강제로 데이터베이스에 반영할 수 있습니다. 하지만 이 방법은 주로 테스트나 특정 프레임워크와 함께 사용할 때 외에는 잘 사용되지 않습니다.

2. **트랜잭션 커밋 시 자동 플러시**:  
   **JPA**는 트랜잭션이 커밋될 때 자동으로 플러시를 호출합니다. 이는 데이터베이스에 변경 사항을 반영하지 않고 트랜잭션만 커밋하는 상황을 방지하기 위한 것입니다. 따라서 트랜잭션 커밋 전에 **Persistence Context**의 변경 내용이 자동으로 데이터베이스에 반영됩니다.

3. **JPQL 쿼리 실행 시 자동 플러시**:  
   **JPQL** 또는 **Criteria**와 같은 객체지향 쿼리를 실행할 때, **Persistence Context**의 변경 사항이 데이터베이스와 동기화되지 않은 상태에서 쿼리를 실행하면, 쿼리 결과가 잘못 나올 수 있습니다. 이 문제를 방지하기 위해 **JPQL** 실행 직전에 자동으로 플러시가 호출됩니다.

#### 예제:

```java
em.persist(memberA);
em.persist(memberB);
em.persist(memberC);

// 중간에 JPQL 실행
query = em.createQuery("select m from Member m", Member.class);
List<Member> members = query.getResultList();
```

이 예제에서 `memberA`, `memberB`, `memberC`는 **Persistence Context**에만 존재하며 아직 데이터베이스에는 반영되지 않았습니다. 이 상태에서 `select` 쿼리를 실행하면, 데이터베이스에 이 세 엔티티가 없으므로 조회되지 않는 문제가 발생할 수 있습니다. 이를 방지하기 위해 **JPQL**이 실행되기 직전에 **Persistence Context**의 변경 내용이 플러시되어 데이터베이스와 동기화됩니다.

### 플러시 모드 옵션

플러시 동작을 제어하기 위해 **javax.persistence.FlushModeType**을 사용하여 플러시 모드를 설정할 수 있습니다.

- **FlushModeType.AUTO**: 트랜잭션 커밋 시 또는 쿼리 실행 시 플러시가 자동으로 호출됩니다. 이는 기본값입니다.
- **FlushModeType.COMMIT**: 트랜잭션을 커밋할 때만 플러시가 발생합니다. 성능 최적화를 위해 사용될 수 있습니다.

```java
em.setFlushMode(FlushModeType.COMMIT); // 플러시 모드를 COMMIT로 설정
```

### 플러시와 Persistence Context

플러시가 수행될 때 **Persistence Context**의 모든 변경 사항이 데이터베이스에 동기화되지만, **Persistence Context** 자체는 초기화되지 않습니다. 다시 말해, 플러시는 **Persistence Context**에서 관리하던 엔티티들을 제거하거나 초기화하지 않습니다. 플러시는 **Persistence Context**의 변경 사항을 데이터베이스에 동기화하는 작업일 뿐, **Persistence Context**는 계속 유지됩니다.

이런 방식으로 데이터베이스와 동기화를 늦출 수 있는 이유는 **트랜잭션** 단위로 작업이 진행되기 때문입니다. **JPA**는 트랜잭션 커밋 직전에만 변경 사항을 데이터베이스에 반영하면 되므로, 그 전까지는 메모리 내에서만 엔티티를 관리하고 필요한 시점에만 데이터베이스에 반영합니다.

### 준영속 상태 (Detached State)

**Persistence Context**에서 더 이상 관리되지 않는 엔티티는 **준영속(Detached)** 상태로 전환됩니다. 이 상태의 엔티티는 **Persistence Context**에서 분리된 상태이기 때문에 **Persistence Context**가 제공하는 기능(변경 감지, 1차 캐시, 쓰기 지연 등)을 사용할 수 없습니다.

### 엔티티를 준영속 상태로 만드는 세 가지 방법

1. **em.detach(entity)**: 특정 엔티티를 **준영속 상태**로 전환합니다.
2. **em.clear()**: **Persistence Context**를 완전히 초기화하여 모든 엔티티를 **준영속 상태**로 만듭니다.
3. **em.close()**: **EntityManager**를 종료하여 **Persistence Context**를 종료하고, 관리하던 모든 엔티티를 **준영속 상태**로 만듭니다.

#### `em.detach()` 메서드:

```java
public void detach(Object entity);
```

`em.detach()` 메서드는 **Persistence Context**에서 특정 엔티티를 분리하여 **준영속 상태**로 만듭니다. 이렇게 분리된 엔티티는 더 이상 **Persistence Context**에서 관리되지 않으며, 변경 감지나 쓰기 지연 등과 같은 기능이 적용되지 않습니다.

```java
Member member = new Member();
member.setId("memberA");
member.setUsername("회원A");

em.persist(member); // 엔티티를 Persistence 상태로 만듦
em.detach(member);  // 엔티티를 준영속 상태로 전환
```

### Persistence Context 초기화: `em.clear()`

`em.clear()`는 **Persistence Context**를 완전히 초기화하여 관리하던 모든 엔티티를 **준영속 상태**로 만듭니다.

```java
Member member = em.find(Member.class, "memberA");
em.clear(); // Persistence Context를 초기화하여 모든 엔티티를 준영속 상태로 만듦

member.setUsername("변경된 이름"); // 준영속 상태이므로 데이터베이스에 반영되지 않음
```

### Persistence Context 종료: `em.close()`
![종료전](./resource/2Flushand/persistence%20context%20제거전.png)
![종료후](./resource/2Flushand/persistence%20context%20제거후.png)

`em.close()`는 **EntityManager**를 종료하고, 관리하던 모든 **Persistence** 상태의 엔티티를 **준영속 상태**로 만듭니다.

```java
EntityManager em = emf.createEntityManager();
Member memberA = em.find(Member.class, "memberA");
em.close(); // Persistence Context 종료, 모든 엔티티가 준영속 상태로 전환
```

### 준영속 상태의 특징

- **Persistence Context**가 관리하지 않으므로 1차 캐시, 변경 감지, 쓰기 지연 등의 기능이 동작하지 않습니다.
- **식별자 값**을 가지고 있습니다. **비영속** 상태의 엔티티는 식별자 값이 없을 수 있지만, 준영속 상태의 엔티티는 한 번 영속 상태였으므로 반드시 식별자 값을 가지고 있습니다.
- **지연 로딩**(Lazy Loading)이 불가능합니다. **Persistence Context**가 더 이상 관리하지 않으므로, 지연 로딩을 시도할 경우 문제가 발생할 수 있습니다.

### 병합: `merge()`

**준영속 상태**의 엔티티를 다시 **Persistence** 상태로 변경하려면 **merge()** 메서드를 사용합니다. **merge()**는 준영속 상태의 엔티티를 받아서 그 정보를 사용해 새로운 **Persistence** 상태의 엔티티를 반환합니다.

#### `merge()` 메서드:

```java
public <T> T merge(T entity);
```

#### `merge()` 사용 예:

```java
Member mergeMember = em.merge(member); // 준영속 상태의 엔티티를 영속 상태로 변경
```

`merge()`는 준영속 상태의 엔티티를 받아 해당 엔티티의 값을 **Persistence Context**에서 관리하는 새로운 영속 엔티티에 복사합니다.

 기존의 준영속 엔티티는 여전히 준영속 상태로 남아있고, 새롭게 병합된 영속 상태의 엔티티가 반환됩니다.

```java
Member member = new Member();
member.setId("memberA");
member.setUsername("회원A");

em.persist(member);
em.detach(member); // 준영속 상태로 전환

Member mergeMember = em.merge(member); // 준영속 엔티티 병합
```

### 병합 동작 순서

1. **준영속 상태**의 `member` 엔티티를 **merge()** 메서드에 전달합니다.
2. **Persistence Context**에서 해당 식별자 값을 가진 엔티티를 찾습니다.
   - 만약 1차 캐시에 없다면, 데이터베이스에서 조회하여 1차 캐시에 저장합니다.
3. **mergeMember**라는 새로운 영속 상태의 엔티티를 생성하여, 준영속 상태의 `member` 엔티티의 값을 복사합니다.
4. 트랜잭션이 커밋될 때, **mergeMember**의 변경 사항이 **변경 감지(Dirty Checking)**에 의해 데이터베이스에 반영됩니다.

### 병합 후 상태

병합이 끝나면 **준영속 상태**의 엔티티는 여전히 준영속 상태로 남아있고, 새롭게 병합된 **영속 상태**의 엔티티가 반환됩니다.

