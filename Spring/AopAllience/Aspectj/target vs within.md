### `@target` 어노테이션

- **정의 및 동작**:
  - `@target`은 AOP 포인트컷 표현식에서 사용되며, 특정 어노테이션이 적용된 **타겟 객체**를 기준으로 조언(Advice)을 적용합니다.
  - 타겟 객체는 프록시가 위임하는 실제 객체입니다. 즉, 스프링 AOP에서 프록시 패턴을 사용하여 메서드 호출을 가로채고 조언을 적용할 때, 프록시가 참조하고 있는 실제 객체에 특정 어노테이션이 적용되어 있는지 확인한 후, 조언을 실행하게 됩니다.

- **사용 예**:
  - 예를 들어, `@Service` 어노테이션이 적용된 모든 클래스의 메서드에 조언을 적용하고 싶을 때 `@target(org.springframework.stereotype.Service)`를 사용할 수 있습니다. 이 경우, 프록시 객체가 위임하는 실제 객체가 `@Service` 어노테이션이 적용된 클래스라면 조언이 실행됩니다.

- **코드 예시**:

  ```java
  @Aspect
  public class LoggingAspect {

      @Before("@target(org.springframework.stereotype.Service)")
      public void logBeforeServiceMethods() {
          System.out.println("Executing method in a service component...");
      }
  }
  ```

  이 예에서는, `@Service` 어노테이션이 붙어 있는 모든 클래스의 메서드가 호출될 때마다 조언이 실행됩니다.

### `@within` 어노테이션

- **정의 및 동작**:
  - `@within`은 AOP 포인트컷 표현식에서 사용되며, 특정 어노테이션이 **클래스 자체에** 적용되어 있는 경우에 조언을 적용합니다.
  - `@within`은 프록시 객체가 아닌 **클래스**의 선언부에 어노테이션이 적용되어 있는지 여부에 따라 조언을 적용합니다. 즉, 클래스 레벨에서 어노테이션이 적용된 모든 메서드에 대해 조언이 적용됩니다.
  - `@within`은 `@target`과 비슷하지만, 포인트컷을 결정하는 기준이 프록시 객체가 아닌 **클래스 자체**입니다.

- **사용 예**:
  - 예를 들어, `@Controller` 어노테이션이 적용된 모든 클래스의 메서드에 대해 조언을 적용하고 싶다면 `@within(org.springframework.stereotype.Controller)`를 사용할 수 있습니다. 이 경우, 클래스 선언부에 `@Controller`가 적용된 모든 클래스의 메서드에 대해 조언이 적용됩니다.

- **코드 예시**:

  ```java
  @Aspect
  public class LoggingAspect {

      @Before("@within(org.springframework.stereotype.Controller)")
      public void logBeforeControllerMethods() {
          System.out.println("Executing method in a controller...");
      }
  }
  ```

  이 예에서는, `@Controller` 어노테이션이 붙어 있는 모든 클래스의 메서드가 호출될 때마다 조언이 실행됩니다.

### `@target` vs `@within`의 차이점

- **적용 기준**:
  - `@target`: 프록시가 위임하는 실제 객체에 적용된 어노테이션을 기준으로 조언이 실행됩니다. 프록시 패턴을 사용하는 환경에서 프록시가 참조하는 실제 클래스에 대한 정보를 바탕으로 동작합니다.
  - `@within`: 클래스 자체에 적용된 어노테이션을 기준으로 조언이 실행됩니다. 클래스 레벨에서 어노테이션이 선언된 경우에만 해당 클래스의 모든 메서드에 조언이 적용됩니다.

- **사용 목적**:
  - `@target`은 프록시 패턴을 사용하여 프록시 객체와 관련된 조언을 적용할 때 유용합니다. 예를 들어, 특정 서비스 클래스에 대한 메서드 호출을 가로채고 싶을 때 적합합니다.
  - `@within`은 특정 어노테이션이 클래스 레벨에 적용된 모든 메서드에 조언을 적용하고자 할 때 유용합니다. 예를 들어, 컨트롤러 레이어의 모든 메서드에 대한 로깅이나 인증을 처리하고자 할 때 사용할 수 있습니다.

### 결론

`@target`과 `@within`은 모두 AOP에서 어노테이션 기반으로 조언을 적용할 때 사용되는 중요한 포인트컷 표현식입니다. `@target`은 프록시 객체가 참조하는 실제 객체를 기준으로 동작하며, `@within`은 클래스 자체에 적용된 어노테이션을 기준으로 동작합니다. 이 두 가지를 잘 이해하고 상황에 맞게 사용하면, 더욱 정교한 AOP 적용이 가능합니다.