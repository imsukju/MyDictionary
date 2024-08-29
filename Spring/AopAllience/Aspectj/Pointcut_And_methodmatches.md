###
스프링의 포인트컷 모델은 advice 유형과 독립적으로 포인트컷을 재사용할 수 있게 해줍니다. 동일한 포인트컷으로 다양한 어드바이스를 타겟팅할 수 있습니다.

org.springframework.aop.Pointcut 인터페이스는 특정 클래스와 메서드를 타겟으로 어드바이스를 지정하기 위해 사용하는 핵심 인터페이스입니다. 전체 인터페이스는 다음과 같습니다:


##  다시 알아보는 Pointcut 개념

Spring에서 **Pointcut**은 특정 메서드나 클래스에 어드바이스를 적용할 위치를 지정하는 방법입니다. 이를 통해 우리는 다양한 어드바이스(Advice)를 동일한 포인트컷(Pointcut)으로 타겟팅할 수 있습니다.


### Pointcut 인터페이스

`Pointcut` 인터페이스는 어드바이스를 어떤 클래스와 메서드에 적용할지 정의하는 데 사용됩니다. 이 인터페이스는 두 가지 핵심 메서드를 가지고 있습니다:

```java
public interface Pointcut {

    ClassFilter getClassFilter();

    MethodMatcher getMethodMatcher();
}
```

이 인터페이스를 통해 클래스와 메서드 매칭 부분을 독립적으로 재사용할 수 있습니다. 또한 다양한 메서드 매처와 결합하여 복잡한 조합 작업을 수행할 수도 있습니다.

### ClassFilter 인터페이스

`ClassFilter` 인터페이스는 포인트컷이 특정 타겟 클래스 집합으로 제한되도록 해줍니다. 이 인터페이스는 다음과 같이 정의됩니다:

```java
public interface ClassFilter {

    boolean matches(Class<?> clazz);
}
```

`matches()` 메서드가 항상 `true`를 반환한다면 모든 타겟 클래스가 일치하게 됩니다. 즉, 모든 클래스가 매칭될 수 있도록 설정할 수 있습니다.

## MethodMatcher 인터페이스
포인트컷에서 메서드 매칭은 일반적으로 클래스 매칭보다 더 중요합니다. Spring AOP에서 메서드 단위로 포인트컷을 정의하는 데 사용되는 중요한 인터페이스가 바로 `MethodMatcher`입니다.


이 메서드들은 각각 다음과 같은 역할을 합니다:

1. **matches(Method m, Class<?> targetClass)**:
   - 특정 클래스의 메서드가 포인트컷의 조건에 맞는지 검사합니다.
   - AOP 프록시가 생성될 때 한 번 실행되며, 이 메서드가 `true`를 반환하면 해당 메서드에 어드바이스가 적용됩니다.
   - 이 과정은 정적 매칭(Static Matching)으로, 메서드 호출 시마다 매칭 로직을 반복할 필요가 없어 성능에 매우 유리합니다.

2. **isRuntime()**:
   - 런타임 시점에서 추가적인 매칭 로직이 필요한지를 결정합니다.
   - `true`를 반환하면, 메서드 호출 시마다 세 개의 아규먼트를 받는 `matches(Method m, Class<?> targetClass, Object... args)` 메서드가 호출됩니다.
   - `false`를 반환하면, 메서드 호출 시 추가적인 매칭이 수행되지 않으며, 캐시된 결과를 사용합니다.

3. **matches(Method m, Class<?> targetClass, Object... args)**:
   - `isRuntime()`이 `true`일 때만 호출되며, 메서드의 실제 아규먼트(`args`)를 포함한 보다 세부적인 매칭을 수행합니다.
   - 이 메서드는 메서드 호출 시마다 실행되어 특정 아규먼트 값에 따라 포인트컷 매칭 여부를 동적으로 결정할 수 있습니다.

### 정적 매칭(Static Matching)과 런타임 매칭(Runtime Matching)

Spring AOP에서 메서드 매칭은 크게 두 가지 방식으로 이루어질 수 있습니다: 정적 매칭과 런타임 매칭입니다.

스프링 AOP(Aspect-Oriented Programming)에서는 포인트컷(Pointcut) 표현식을 사용하여 어떤 메서드에 어드바이스(Advice)를 적용할지를 결정합니다. 포인트컷 표현식의 평가 시점에 따라 **정적 매칭(Static Matching)**과 **런타임 매칭(Runtime Matching)**으로 나눌 수 있습니다. 이 두 가지 매칭 방식은 스프링 AOP의 동작 방식을 이해하는 데 중요한 개념입니다.

#Spring AOP에서 정적 매칭과 런타임 매칭의 차이를 이해하기 위해 간단한 예제 코드를 살펴보겠습니다.

### 1. 정적 매칭 예제

먼저, 정적 매칭의 예제를 살펴보겠습니다. 여기서 `execution` 포인트컷 표현식을 사용하여 특정 메서드 패턴에 매칭하는 방식입니다.

#### 예제 코드:

```java
// Service 클래스
package com.example.service;

import org.springframework.stereotype.Service;

@Service
public class MyService {
    public void performTask() {
        System.out.println("Performing task...");
    }

    public void performAnotherTask() {
        System.out.println("Performing another task...");
    }
}

// Aspect 클래스
package com.example.aspect;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class MyAspect {

    // execution 포인트컷: com.example.service 패키지의 모든 메서드에 적용
    @Before("execution(* com.example.service.*.*(..))")
    public void beforeAdvice() {
        System.out.println("Before method execution - static matching");
    }
}
```

#### 설명:
- **정적 매칭:** `execution(* com.example.service.*.*(..))` 포인트컷 표현식은 `com.example.service` 패키지 내의 모든 메서드에 적용됩니다. 이 매칭은 애플리케이션이 시작될 때 결정됩니다.
- `MyService` 클래스의 모든 메서드(`performTask`, `performAnotherTask`)는 이 `Aspect`가 적용됩니다. 즉, 이 메서드들이 호출될 때마다 `beforeAdvice` 메서드가 먼저 실행됩니다.
- 이 방식은 메서드의 이름이나 시그니처 등 정적인 정보를 기반으로 필터링됩니다.

### 2. 런타임 매칭 예제

이번에는 런타임 매칭의 예제를 살펴보겠습니다. 여기서는 `@annotation` 포인트컷 표현식을 사용하여 메서드에 특정 어노테이션이 존재하는지를 기준으로 AOP를 적용합니다.

#### 예제 코드:

```java
// Service 클래스
package com.example.service;

import org.springframework.stereotype.Service;

@Service
public class MyService {

    @MyCustomAnnotation
    public void performTask() {
        System.out.println("Performing task...");
    }

    public void performAnotherTask() {
        System.out.println("Performing another task...");
    }
}

// Custom Annotation
package com.example.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface MyCustomAnnotation {
}

// Aspect 클래스

@Aspect
@Component
public class MyAspect {

    // @annotation 포인트컷: MyCustomAnnotation 어노테이션이 있는 메서드에만 적용
    @Before("@annotation(com.example.annotation.MyCustomAnnotation)")
    public void beforeAdvice() {
        System.out.println("Before method execution - runtime matching");
    }
}
```

#### 설명:
- **런타임 매칭:** `@annotation(com.example.annotation.MyCustomAnnotation)` 포인트컷 표현식은 메서드에 `MyCustomAnnotation` 어노테이션이 붙어 있는지를 기준으로 AOP를 적용합니다. 
- `performTask` 메서드에 `MyCustomAnnotation` 어노테이션이 붙어 있기 때문에, 이 메서드가 호출될 때만 `beforeAdvice` 메서드가 실행됩니다. 
- 이 매칭은 메서드가 호출되는 시점에 어노테이션이 실제로 존재하는지를 검사하는 과정에서 런타임 매칭이 이루어집니다.

### 요약
- **정적 매칭:** 메서드의 시그니처나 클래스 타입과 같은 정적 정보에 기반하여 AOP가 적용됩니다. 애플리케이션 시작 시점에 대상이 결정됩니다.
- **런타임 매칭:** 메서드의 실행 시점에서 동적으로 조건을 검사하여 AOP가 적용됩니다. 실행 중에 런타임 정보를 바탕으로 대상이 결정됩니다.

이 두 가지 매칭 방식은 AOP를 적용하는 시점과 유연성에 차이가 있으며, 각각의 상황에 맞게 사용할 수 있습니다.


### 포인트컷 캐싱의 중요성

정적 매칭을 사용할 경우, AOP 프레임워크가 AOP 프록시가 생성될 때 포인트컷 평가 결과를 캐시할 수 있습니다. 이를 통해 메서드 호출 시 매칭을 다시 수행할 필요가 없어 성능이 크게 개선될 수 있습니다.

## 결론

Spring AOP의 `MethodMatcher`와 `Pointcut` API는 매우 강력하면서도 유연하게 설계되어 있습니다. 정적 매칭을 통해 성능을 극대화하고, 필요에 따라 런타임 매칭을 활용하여 동적인 조건을 처리할 수 있습니다. 이와 같은 설계를 통해 Spring AOP는 다양한 시나리오에서 효율적으로 동작할 수 있습니다.

Spring AOP에서 포인트컷을 이해하고 활용하는 것은 어드바이스를 적절히 적용하고, 성능을 최적화하는 데 중요한 역할을 합니다. 가능하면 정적 매칭을 활용하여 AOP 프레임워크가 효율적으로 동작하도록 설계하는 것이 좋습니다.