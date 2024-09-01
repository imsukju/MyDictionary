### 1. `execution()`
- **설명**: 특정 메서드의 실행을 매칭합니다. 메서드의 이름, 리턴 타입, 파라미터 등을 기준으로 조언을 적용할 수 있습니다.
- **예시**: `execution(* transfer(..))` - 이름이 `transfer`인 모든 메서드의 실행을 매칭합니다.

### 2. `within()`
- **설명**: 특정 클래스 또는 패키지 내의 모든 메서드를 매칭합니다.
- **예시**: `within(com.AspectjDemo.One.declareingpointcut.service.TransferService)` - `TransferService` 클래스 내의 모든 메서드를 매칭합니다.

### 3. `this()`
- **설명**: 프록시 객체가 특정 타입을 구현하는 경우에 매칭합니다. 주로 Spring AOP에서 사용되며, 프록시 객체의 타입을 기준으로 조언을 적용합니다.
- **예시**: `this(com.AspectjDemo.One.declareingpointcut.service.TransferService)` - 프록시 객체가 `TransferService` 타입을 구현하는 경우 매칭합니다.

### 4. `target()`
- **설명**: 프록시가 위임하는 실제 타겟 객체가 특정 타입을 구현하는 경우에 매칭합니다.
- **예시**: `target(com.AspectjDemo.One.declareingpointcut.service.SpecialService)` - 실제 타겟 객체가 `SpecialService` 타입인 경우 매칭합니다.

### 5. `args()`
- **설명**: 특정 타입의 아규먼트를 받는 메서드를 매칭합니다.
- **예시**: `args(String, ..)` - 첫 번째 파라미터가 `String` 타입인 모든 메서드를 매칭합니다.

### 6. `@annotation()`
- **설명**: 특정 어노테이션이 적용된 메서드를 매칭합니다.
- **예시**: `@annotation(com.AspectjDemo.One.declareingpointcut.annotation.Loggable)` - `@Loggable` 어노테이션이 붙은 메서드를 매칭합니다.

### 7. `@within()`
- **설명**: 특정 어노테이션이 클래스 레벨에 적용된 경우 그 클래스의 모든 메서드를 매칭합니다.
- **예시**: `@within(com.AspectjDemo.One.declareingpointcut.annotation.SpecialComponent)` - `@SpecialComponent` 어노테이션이 적용된 클래스 내의 모든 메서드를 매칭합니다.

### 8. `@target()`
- **설명**: 특정 어노테이션이 적용된 타입의 타겟 객체를 기준으로 매칭합니다.
- **예시**: `@target(com.AspectjDemo.One.declareingpointcut.annotation.Validated)` - `@Validated` 어노테이션이 적용된 객체의 메서드를 매칭합니다.

### 9. `@args()`
- **설명**: 메서드의 파라미터가 특정 어노테이션을 가진 타입인 경우 매칭합니다.
- **예시**: `@args(com.AspectjDemo.One.declareingpointcut.annotation.Validated)` - 메서드의 아규먼트가 `@Validated` 어노테이션을 가진 타입인 경우 매칭합니다.

### 결론

이러한 포인트컷 표현식들은 AOP를 적용할 때 다양한 조건에 따라 메서드 호출을 필터링하고, 그에 맞는 조언을 적용할 수 있도록 도와줍니다. 이를 통해 코드의 관심사를 분리하고, 특정 동작을 다양한 방식으로 조작하거나 모니터링할 수 있습니다.