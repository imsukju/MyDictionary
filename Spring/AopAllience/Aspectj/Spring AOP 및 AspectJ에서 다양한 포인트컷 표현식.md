
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


## SpringAoP와 AspectJ 의 표현식 차이

### 1. AspectJ 표현식
AspectJ는 다양한 조인 포인트(Join Point)를 지정할 수 있는 강력한 표현식을 제공합니다. 대표적인 표현식은 다음과 같습니다:

- **execution()**: 메소드 실행 조인 포인트를 지정합니다.
  ```java
  execution(* com.example.service.*.*(..))
  ```
  `com.example.service` 패키지 내의 모든 클래스의 모든 메소드를 타겟으로 합니다.

- **within()**: 특정 타입 내에 있는 조인 포인트를 지정합니다.
  ```java
  within(com.example.service..*)
  ```
  `com.example.service` 패키지와 그 하위 패키지의 모든 클래스에 적용됩니다.

- **this()**: 프록시 객체의 타입을 기준으로 조인 포인트를 지정합니다.
  ```java
  this(com.example.service.MyService)
  ```
  `MyService` 타입의 프록시 객체에서 실행되는 모든 메소드에 적용됩니다.

- **target()**: 타겟 객체의 타입을 기준으로 조인 포인트를 지정합니다.
  ```java
  target(com.example.service.MyService)
  ```
  `MyService` 타입의 타겟 객체에서 실행되는 모든 메소드에 적용됩니다.

- **args()**: 메소드의 인자 타입을 기준으로 조인 포인트를 지정합니다.
  ```java
  args(java.lang.String)
  ```
  `String` 타입의 인자를 받는 모든 메소드에 적용됩니다.

- **@annotation()**: 특정 어노테이션이 적용된 메소드에 조인 포인트를 지정합니다.
  ```java
  @annotation(com.example.annotation.Loggable)
  ```
  `Loggable` 어노테이션이 붙은 모든 메소드에 적용됩니다.

- **@within()**: 특정 어노테이션이 적용된 타입 내의 모든 메소드에 조인 포인트를 지정합니다.
  ```java
  @within(com.example.annotation.Loggable)
  ```
  `Loggable` 어노테이션이 붙은 클래스의 모든 메소드에 적용됩니다.

### 2. Spring AOP 표현식
Spring AOP는 AspectJ와 유사한 표현식을 사용하지만, 기능적으로 제한적입니다. 주로 메소드 실행 조인 포인트에 집중합니다.

- **execution()**: 메소드 실행 조인 포인트를 지정합니다.
  ```java
  execution(* com.example.service.*.*(..))
  ```
  `com.example.service` 패키지 내의 모든 클래스의 모든 메소드를 타겟으로 합니다. AspectJ의 `execution` 표현식과 동일하게 동작합니다.

- **within()**: 특정 타입 내의 조인 포인트를 지정합니다.
  ```java
  within(com.example.service..*)
  ```
  `com.example.service` 패키지와 그 하위 패키지의 모든 클래스에 적용됩니다.

- **this()**: 프록시 객체의 타입을 기준으로 조인 포인트를 지정합니다. 그러나 Spring AOP에서는 이 표현식이 제한적으로 지원됩니다.
  ```java
  this(com.example.service.MyService)
  ```
  `MyService` 타입의 프록시 객체에서 실행되는 모든 메소드에 적용됩니다.

- **target()**: 타겟 객체의 타입을 기준으로 조인 포인트를 지정합니다.
  ```java
  target(com.example.service.MyService)
  ```
  `MyService` 타입의 타겟 객체에서 실행되는 모든 메소드에 적용됩니다.

- **args()**: 메소드의 인자 타입을 기준으로 조인 포인트를 지정합니다.
  ```java
  args(java.lang.String)
  ```
  `String` 타입의 인자를 받는 모든 메소드에 적용됩니다.

- **@annotation()**: 특정 어노테이션이 적용된 메소드에 조인 포인트를 지정합니다.
  ```java
  @annotation(com.example.annotation.Loggable)
  ```
  `Loggable` 어노테이션이 붙은 모든 메소드에 적용됩니다.

### 차이점 요약

- **포인트컷 범위**: AspectJ는 필드 접근, 생성자 호출 등 더 넓은 범위의 포인트컷을 지원하지만, Spring AOP는 주로 메소드 실행 조인 포인트에 제한됩니다.
- **표현식**: 두 프레임워크 모두 유사한 표현식을 사용하지만, AspectJ는 더 강력한 기능을 제공하며, 다양한 조인 포인트를 다룰 수 있습니다.
- **적용 범위**: AspectJ는 모든 자바 클래스에 적용 가능하지만, Spring AOP는 스프링 컨테이너에서 관리하는 빈에만 적용됩니다.

이처럼 같은 표현식을 사용하더라도 두 프레임워크에서 동작하는 방식과 적용 범위가 다를 수 있습니다. AspectJ는 더 강력하고 세부적인 AOP 기능이 필요한 경우에 적합하며, Spring AOP는 스프링 애플리케이션 내에서 간단하게 AOP를 적용하고자 할 때 유용합니다.
### 결론

이러한 포인트컷 표현식들은 AOP를 적용할 때 다양한 조건에 따라 메서드 호출을 필터링하고, 그에 맞는 조언을 적용할 수 있도록 도와줍니다. 이를 통해 코드의 관심사를 분리하고, 특정 동작을 다양한 방식으로 조작하거나 모니터링할 수 있습니다.