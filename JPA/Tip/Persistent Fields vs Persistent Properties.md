### Persistent Fields

엔티티 클래스가 Persistent Fields를 사용하는 경우, Persistence 런타임은 엔티티 클래스의 인스턴스 변수에 직접 접근합니다. `javax.persistence.Transient`로 주석 처리되지 않았거나 자바에서 `transient`로 표시되지 않은 모든 필드는 데이터 저장소에 저장됩니다. 객체/관계형 매핑 어노테이션은 인스턴스 변수에 적용되어야 합니다.

### Persistent Properties

엔티티가 Persistent Properties를 사용하는 경우, 해당 엔티티는 JavaBeans 컴포넌트의 메서드 규칙을 따라야 합니다. JavaBeans 스타일의 프로퍼티는 보통 엔티티 클래스의 인스턴스 변수 이름에 따라 지어진 getter와 setter 메서드를 사용합니다. 엔티티의 모든 Persistent Property `property`가 `Type` 타입이라면, 해당 프로퍼티에 대한 getter 메서드 `getProperty`와 setter 메서드 `setProperty`가 존재해야 합니다. 만약 프로퍼티가 `Boolean` 타입이라면, `getProperty` 대신 `isProperty`를 사용할 수 있습니다.

예를 들어, `Customer` 엔티티가 Persistent Properties를 사용하고 `firstName`이라는 private 인스턴스 변수를 가지고 있다면, 이 클래스는 `firstName` 변수를 조회하고 설정하기 위해 `getFirstName`과 `setFirstName` 메서드를 정의해야 합니다.

단일 값 Persistent Properties에 대한 메서드 시그니처는 다음과 같습니다:

```
Type getProperty()
void setProperty(Type type)
```

Persistent Properties에 대한 객체/관계형 매핑 어노테이션은 getter 메서드에 적용되어야 하며, `@Transient`로 주석 처리되었거나 `transient`로 표시된 필드 또는 프로퍼티에는 매핑 어노테이션을 적용할 수 없습니다.

---

이 방식에서, 필드에 직접 접근하는 Persistent Fields와 getter/setter를 사용하는 Persistent Properties 간의 차이를 명확히 이해하는 것이 중요합니다.