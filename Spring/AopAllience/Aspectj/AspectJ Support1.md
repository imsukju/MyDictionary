@AspectJ 스타일은 자바 클래스에서 애스펙트를 정의하기 위해 애노테이션을 사용하는 방법을 의미하며, AspectJ 5 릴리스에서 도입된 것입니다. Spring 프레임워크는 이러한 @AspectJ 스타일 애노테이션을 지원하여, 개발자가 AspectJ와 유사한 방식으로 애스펙트를 정의할 수 있도록 합니다. 이때 Spring은 AspectJ 라이브러리를 사용해 포인트컷을 해석하지만, 런타임에는 순수한 Spring AOP를 사용하며 AspectJ 컴파일러나 위버에 의존하지 않습니다.

### AspectJ 컴파일러의 역할
AspectJ는 자바의 확장으로, AOP(Aspect-Oriented Programming)를 지원하는 라이브러리입니다. AspectJ는 자바 소스 코드를 처리하는 AspectJ 컴파일러(AJC)를 사용하여 어노테이션과 포인트컷을 분석하고, 컴파일 타임에 이를 바이트코드에 적용합니다.

>### AspectJ에서 사용하는 바이트 코드 조작 라이브러리
>BCEL (Byte Code Engineering Library): AspectJ의 초기 버전에서는 BCEL을 사용하여 바이트 코드를 조작했습니다. BCEL은 바이트코드를 생성, 분석, 수정하는 데 사용되는 라이브러리입니다.
>ASM: AspectJ의 최신 버전에서는 ASM을 사용하여 바이트 코드를 조작합니다. ASM은 빠르고 가벼운 바이트 코드 조작 라이브러리로, AspectJ에서 위빙(Weaving) 프로세스를 수행하는 데 사용됩니다.
>이 두 라이브러리는 AspectJ의 컴파일러(Ajc)와 런타임에 AspectJ의 위빙 기능을 지원하기 위해 사용됩니다. BCEL은 AspectJ의 초기에 사용되었으나, 성능과 유지보수의 이유로 ASM으로 대체되었습니다.

### AspectJ vs. Spring AOP

- **AspectJ**: 완전한 AOP 프레임워크로, 컴파일 시, 컴파일 후(바이너리 위빙), 그리고 로드 타임 위빙을 제공합니다. 모든 Java 객체에 애스펙트를 적용할 수 있으며, 강력하고 광범위한 기능을 제공합니다.
- **Spring AOP**: Spring 프레임워크의 일부로, 프록시 기반 AOP를 제공합니다. 주로 Spring 빈에 대한 메서드 수준에서 인터셉션을 지원하며, AspectJ의 일부 기능을 제공합니다.

### @AspectJ 스타일

@AspectJ 스타일은 Spring에서 AspectJ 어노테이션(@Aspect, @Before, @After 등)을 사용하여 애스펙트를 정의하는 방식을 의미합니다. 이는 XML 구성 없이 애노테이션만으로 애스펙트를 선언적으로 작성할 수 있게 해줍니다.

### 주요 AspectJ 어노테이션 (Spring AOP에서 사용됨)

- **@Aspect**: 클래스를 애스펙트로 표시합니다.
- **@Before**: 특정 메서드 실행 전에 advice를 정의합니다.
- **@After**: 메서드 실행 후에 advice를 정의합니다.
- **@Around**: 메서드 실행 과정을 제어하는 advice를 정의합니다.
- **@AfterReturning**: 메서드가 성공적으로 반환된 후에 advice를 정의합니다.
- **@AfterThrowing**: 메서드가 예외를 던질 때 advice를 정의합니다.
- **@Pointcut**: 재사용 가능한 포인트컷을 정의합니다.

### 요약

- "AspectJ 스타일"은 Spring 내에서 AspectJ 어노테이션을 사용하여 애스펙트를 정의하는 방법을 의미합니다.
- @AspectJ라는 애노테이션 자체는 없지만, AspectJ의 어노테이션을 사용하여 Spring AOP에서 애스펙트를 정의할 수 있습니다.
- 이 접근 방식은 더 선언적이고 간결한 코드 작성을 가능하게 합니다.


### @AspectJ Support 활성화 및 Spring AOP의 Auto-Proxying

Spring AOP에서 @AspectJ를 사용하여 애플리케이션에 관점을 적용하려면 몇 가지 설정이 필요합니다. 이 과정에서 핵심 개념인 "auto-proxying"도 함께 이해해야 합니다. 이를 쉽게 설명하면 다음과 같습니다:

#### 1. @AspectJ Support 활성화
@AspectJ는 Spring AOP에서 사용하는 애노테이션 기반의 관점 지향 프로그래밍(Aspect-Oriented Programming) 지원 방식입니다. 이를 활성화하려면 `@EnableAspectJAutoProxy` 애노테이션을 사용해야 합니다. 이 애노테이션을 통해 Spring이 @AspectJ 스타일로 작성된 aspect를 인식하고, 이 aspect에 정의된 advice를 애플리케이션의 적절한 지점에 적용할 수 있게 됩니다.

#### 2. Auto-Proxying이란?
Auto-Proxying은 Spring이 특정 빈(Bean)에 대해 자동으로 프록시를 생성하는 기능입니다. 프록시란 메서드 호출을 가로채고, 필요한 경우 추가적인 advice을 실행할 수 있는 중간 대리자 역할을 합니다.

Spring은 애플리케이션에서 빈을 생성할 때, 해당 빈이 하나 이상의 aspect에 의해 영향을 받는지를 확인합니다. 만약 해당 빈이 aspect에 의해 어드바이스(advice)된다면, Spring은 자동으로 그 빈에 대한 프록시를 생성합니다. 이렇게 생성된 프록시는 메서드 호출을 가로채어, 설정된 advice를 실행하거나 원래의 메서드를 호출하는 역할을 합니다.

#### 3. 실제 동작 방식
예를 들어, 로그를 기록하는 aspect를 만들었다고 가정해봅시다. 이 aspect는 특정 메서드가 호출될 때마다 로그를 남기도록 설정되었습니다. Spring이 이 aspect를 감지하면, 해당 메서드를 포함하는 빈에 대해 자동으로 프록시를 생성합니다. 이제 이 빈의 메서드가 호출될 때마다 프록시가 먼저 개입하여 로그를 남기고, 이후에 원래의 메서드를 실행하게 됩니다.

이러한 auto-proxying 덕분에 개발자는 코드 변경 없이도 AOP 설정만으로 다양한 기능(예: 로그, 트랜잭션 관리 등)을 쉽게 적용할 수 있습니다.

#### 4. 정리
- **@AspectJ Support 활성화:** `@EnableAspectJAutoProxy` 애노테이션을 사용하여 Spring에서 @AspectJ 기반의 AOP를 활성화합니다.
- **Auto-Proxying:** Spring이 특정 빈이 aspect에 의해 어드바이스되는지 여부를 판단한 후, 필요시 자동으로 프록시를 생성하여 메서드 호출을 가로채고 advice를 실행합니다.

이러한 개념을 이해하면 Spring AOP를 보다 쉽게 적용하고 활용할 수 있을 것입니다. Spring의 auto-proxying 기능 덕분에 애플리케이션의 로직과 별도로 부가적인 기능을 깔끔하게 분리하여 관리할 수 있습니다.

### Autodetecting Aspects through Component Scanning

Spring 프레임워크에서는 애스펙트(Aspect)를 자동으로 감지하고 적용하기 위해 여러 가지 방법을 사용할 수 있습니다. 하지만 @Aspect 애노테이션만으로는 클래스패스 스캔을 통해 자동으로 감지되지 않을 수 있습니다. 이를 보다 확실하게 하기 위해서는 몇 가지 추가적인 설정이 필요합니다. 

#### 1. 클래스패스 스캔을 통한 자동 감지

Spring에서 애스펙트를 자동으로 감지하려면 클래스패스 스캔을 사용해야 합니다. 이를 위해 Spring은 특정 패키지를 스캔하여 @Component 애노테이션이 달린 클래스들을 찾아 빈으로 등록합니다. 그러나 애스펙트 클래스에 @Aspect 애노테이션만을 붙인 경우, Spring은 해당 클래스를 스캔하고 빈으로 등록하지 않습니다. 

#### 2. @Component 애노테이션의 필요성

클래스패스에서 애스펙트를 자동으로 감지하려면, @Aspect 애노테이션과 함께 @Component 애노테이션을 사용해야 합니다. @Component는 Spring이 해당 클래스를 빈으로 인식하고 관리하게 합니다. 즉, @Aspect만 사용한 클래스는 Spring의 스캐너에 의해 자동으로 감지되지 않지만, @Component를 추가하면 Spring은 이를 빈으로 등록하고, AOP 설정에 따라 애스펙트를 적용합니다.

#### 3. 대체 방법: XML 설정이나 @Configuration 클래스 사용

애스펙트를 Spring 컨텍스트에 등록하는 다른 방법으로는 Spring XML 설정 파일에서 명시적으로 빈으로 등록하거나, @Configuration 클래스를 사용하여 @Bean 메서드를 통해 애스펙트를 등록하는 방법이 있습니다. 이 경우 @Component 애노테이션을 사용하지 않아도 되지만, 수동으로 애스펙트를 관리해야 합니다.

**Advising aspects with other aspects?**  
Spring AOP에서 aspect 자체는 다른 aspect의 어드바이스 대상이 될 수 없습니다. 클래스에 @Aspect 애노테이션이 붙으면 해당 클래스는 aspect로 표시되며, 따라서 auto-proxying 대상에서 제외됩니다.


### Supported Pointcut Designators(지시어)
> @Pointcut Annotation을 사용하면 Annotation이 붙은 메소드를 Alias(별명 혹은 별칭)으로 활용 할
#### 1. execution

```java
@Aspect
public class LoggingAspect {

    @Pointcut("execution(* com.example.service.*.*(..))")
    private void allServiceMethods() {}

    @Before("allServiceMethods()")
    public void logBeforeMethodExecution() {
        System.out.println("Method is about to execute.");
    }
}
```
**설명:** `allServiceMethods` 메서드는 `execution` 포인트컷을 정의하며, 이를 다른 advice에서 포인트컷 표현식으로 사용합니다.

#### 2. within

```java
@Aspect
public class SecurityAspect {

    @Pointcut("within(com.example.controller..*)")
    private void controllerLayer() {}

    @Before("controllerLayer()")
    public void checkSecurity() {
        System.out.println("Security check before method execution.");
    }
}
```
**설명:** `controllerLayer` 메서드는 `within` 포인트컷을 정의하고, 이 포인트컷을 다른 advice에서 재사용합니다.

#### 3. this

```java
@Aspect
public class PerformanceAspect {

    @Pointcut("this(com.example.service.MyService)")
    private void proxyIsMyService() {}

    @Before("proxyIsMyService()")
    public void logPerformance() {
        System.out.println("Logging performance data.");
    }
}
```
**설명:** `proxyIsMyService` 메서드는 `this` 포인트컷을 정의하며, 이를 사용하여 프록시가 `MyService` 타입일 때 로그를 기록합니다.

#### 4. target

```java
@Aspect
public class AuditAspect {

    @Pointcut("target(com.example.service.OrderService)")
    private void targetIsOrderService() {}

    @Before("targetIsOrderService()")
    public void auditMethodExecution() {
        System.out.println("Auditing OrderService method.");
    }
}
```
**설명:** `targetIsOrderService` 메서드는 `target` 포인트컷을 정의하고, 이 포인트컷을 재사용하여 감사를 수행합니다.

#### 5. args

```java
@Aspect
public class ValidationAspect {

    @Pointcut("args(java.lang.String, ..)")
    private void stringArgumentMethods() {}

    @Before("stringArgumentMethods()")
    public void validateStringArgument() {
        System.out.println("Validating String argument.");
    }
}
```
**설명:** `stringArgumentMethods` 메서드는 `args` 포인트컷을 정의하고, 이를 통해 문자열 인자를 가지는 메서드를 대상으로 검증을 수행합니다.

#### 6. @target

```java
@Aspect
public class TransactionAspect {

    @Pointcut("@target(org.springframework.transaction.annotation.Transactional)")
    private void transactionalTarget() {}

    @Before("transactionalTarget()")
    public void manageTransaction() {
        System.out.println("Managing transaction.");
    }
}
```
**설명:** `transactionalTarget` 메서드는 `@target` 포인트컷을 정의하며, 이를 통해 `@Transactional` 애노테이션이 있는 클래스의 메서드에 대해 트랜잭션을 관리합니다.

#### 7. @args

```java
@Aspect
public class LogAspect {

    @Pointcut("@args(com.example.annotation.Loggable)")
    private void loggableArguments() {}

    @Before("loggableArguments()")
    public void logAnnotatedArgument() {
        System.out.println("Logging argument with @Loggable annotation.");
    }
}
```
**설명:** `loggableArguments` 메서드는 `@args` 포인트컷을 정의하고, 이를 사용하여 `@Loggable` 애노테이션이 있는 인자가 있는 메서드를 대상으로 로그를 기록합니다.

#### 8. @within

```java
@Aspect
public class MonitoringAspect {

    @Pointcut("@within(org.springframework.stereotype.Service)")
    private void serviceLayer() {}

    @Before("serviceLayer()")
    public void monitorServiceLayer() {
        System.out.println("Monitoring service layer.");
    }
}
```
**설명:** `serviceLayer` 메서드는 `@within` 포인트컷을 정의하고, 이를 통해 `@Service` 애노테이션이 붙은 클래스 내의 메서드를 대상으로 모니터링을 수행합니다.

#### 9. @annotation

```java
@Aspect
public class AccessControlAspect {

    @Pointcut("@annotation(com.example.annotation.Secure)")
    private void secureMethod() {}

    @Before("secureMethod()")
    public void checkAccess() {
        System.out.println("Checking access control.");
    }
}
```
**설명:** `secureMethod` 메서드는 `@annotation` 포인트컷을 정의하고, 이를 통해 `@Secure` 애노테이션이 붙은 메서드에 대해 접근 제어를 수행합니다.

---
