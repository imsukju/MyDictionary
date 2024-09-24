
"[공식 문서](http://querydsl.com/static/querydsl/5.0.0/reference/html_single/)를 기반으로 내용을 재정리하여 글을 작성했습니다."

### QueryDSL 5.0.0 레퍼런스 가이드

`QueryDSL`은 여러 데이터 소스에 대해 타입 안전한 쿼리를 작성할 수 있는 도구입니다. 이를 통해 자바와 같은 프로그래밍 언어에서 SQL 또는 JPQL 쿼리를 직접 작성할 때 발생할 수 있는 오류를 방지할 수 있습니다. 이 가이드는 QueryDSL을 어떻게 설치하고 사용하는지에 대한 자세한 내용을 다룹니다.

#### 1. QueryDSL 설정

QueryDSL을 사용하기 위해서는 프로젝트에 적절한 의존성을 추가해야 합니다. 이는 Gradle이나 Maven과 같은 빌드 도구를 통해 쉽게 설정할 수 있습니다.

**Gradle 설정 예시:**

```gradle
dependencies {
    implementation "com.querydsl:querydsl-jpa:5.0.0"
    annotationProcessor "com.querydsl:querydsl-apt:5.0.0:jpa"
}
```

**Maven 설정 예시:**

```xml
<dependency>
    <groupId>com.querydsl</groupId>
    <artifactId>querydsl-jpa</artifactId>
    <version>5.0.0</version>
</dependency>
<dependency>
    <groupId>com.querydsl</groupId>
    <artifactId>querydsl-apt</artifactId>
    <version>5.0.0</version>
    <scope>provided</scope>
</dependency>
```

설정이 완료되면 엔티티 클래스에 대한 Q타입 클래스가 자동으로 생성됩니다. 이 Q타입은 QueryDSL에서 쿼리 작성 시 필드와 메소드를 제공하는 중요한 역할을 합니다.

#### 2. QueryDSL을 이용한 기본 쿼리 작성

QueryDSL을 사용하면 JPA나 SQL을 더욱 직관적이고 안전하게 작성할 수 있습니다. 기본적으로 QueryDSL은 `JPAQueryFactory`를 사용해 쿼리를 생성하며, 이 객체는 `EntityManager`를 필요로 합니다.

**예시:**

```java
JPAQueryFactory queryFactory = new JPAQueryFactory(entityManager);
QMember member = QMember.member;

List<Member> members = queryFactory
    .selectFrom(member)
    .where(member.name.eq("John"))
    .fetch();
```

위 코드는 `name`이 "John"인 멤버를 조회하는 간단한 쿼리입니다. `where` 절에 조건을 추가할 때는 필드 이름과 메서드를 통해 안전하게 질의를 작성할 수 있습니다.

#### 3. 고급 쿼리 작성

QueryDSL은 복잡한 쿼리도 간결하게 작성할 수 있습니다. 여러 조건을 결합하거나 조인을 수행할 때도 가독성이 유지되며, 코드 유지보수가 용이해집니다.

**복합 조건 예시:**

```java
BooleanExpression isActive = member.status.eq(Status.ACTIVE);
BooleanExpression isOlderThan30 = member.age.gt(30);

List<Member> result = queryFactory
    .selectFrom(member)
    .where(isActive.and(isOlderThan30))
    .fetch();
```

이 코드는 회원이 활성 상태이며 나이가 30세 이상인 경우에만 결과를 반환합니다.

**조인 예시:**

```java
QOrder order = QOrder.order;

List<Tuple> result = queryFactory
    .select(member, order)
    .from(member)
    .leftJoin(order).on(order.member.id.eq(member.id))
    .fetch();
```

이 쿼리는 `Member`와 `Order` 엔티티를 조인하는 예시로, 조인을 통해 두 테이블의 데이터를 동시에 조회할 수 있습니다.

#### 4. 서브쿼리와 집계 함수

QueryDSL은 SQL에서 흔히 사용하는 서브쿼리와 집계 함수를 지원합니다. 이를 통해 더 복잡한 데이터 조회가 가능합니다.

**서브쿼리 예시:**

```java
QOrder order = QOrder.order;

List<Member> result = queryFactory
    .selectFrom(member)
    .where(member.id.in(
        JPAExpressions.select(order.member.id)
            .from(order)
            .where(order.amount.gt(100))
    ))
    .fetch();
```

위 예시는 주문 금액이 100 이상인 주문을 한 회원을 조회하는 서브쿼리를 포함한 예시입니다.

**집계 함수 예시:**

```java
List<Tuple> result = queryFactory
    .select(member.age, member.count())
    .from(member)
    .groupBy(member.age)
    .fetch();
```

이 코드는 나이별로 회원 수를 조회하는 예시로, `groupBy`와 같은 집계 함수도 쉽게 사용할 수 있습니다.

#### 5. QueryDSL의 동적 쿼리

QueryDSL은 동적 쿼리 작성에도 유리합니다. `BooleanBuilder`를 사용하면 조건을 런타임에 동적으로 추가하거나 제거할 수 있어 유연한 쿼리 작성이 가능합니다.

**동적 쿼리 예시:**

```java
BooleanBuilder builder = new BooleanBuilder();
if (name != null) {
    builder.and(member.name.eq(name));
}
if (status != null) {
    builder.and(member.status.eq(status));
}

List<Member> result = queryFactory
    .selectFrom(member)
    .where(builder)
    .fetch();
```

위 코드는 `name`과 `status` 값에 따라 조건을 동적으로 추가하는 예시입니다.

---

### 마무리

`QueryDSL`은 자바 개발자가 SQL 또는 JPQL을 더욱 안전하고 가독성 있게 작성할 수 있도록 도와주는 프레임워크입니다. 컴파일 타임에 오류를 검출할 수 있어 쿼리 작성 시 발생할 수 있는 문제를 방지할 수 있으며, 메소드 체이닝 방식으로 직관적인 쿼리 작성이 가능합니다. QueryDSL을 사용하면 유지보수와 확장성이 뛰어난 코드를 작성할 수 있어 대규모 프로젝트에서도 효율적으로 사용할 수 있습니다.

--- 

이로써 QueryDSL 5.0.0 레퍼런스의 주요 내용을 한국어로 번역 및 재구성해보았습니다.