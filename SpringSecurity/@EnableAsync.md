`@EnableAsync`는 Spring에서 비동기 작업을 수행할 수 있도록 활성화하는 어노테이션입니다. 이 어노테이션을 사용하면 `@Async`가 붙은 메서드가 별도의 쓰레드에서 실행될 수 있게 해주며, 이를 통해 애플리케이션의 응답 속도를 향상시키거나, 시간이 오래 걸리는 작업을 백그라운드에서 수행할 수 있습니다.

### 1. `@EnableAsync`의 기본 역할
`@EnableAsync`는 **비동기 작업을 활성화**하여 `@Async`로 표시된 메서드를 비동기로 실행할 수 있도록 Spring의 스케줄러 및 쓰레드 풀을 설정해 줍니다.
- **비동기**: 요청을 받으면 즉시 응답을 반환하고, 특정 작업을 별도의 쓰레드에서 수행합니다.
- `@Async`가 적용된 메서드를 호출하면, 해당 메서드는 **기본적으로 별도의 쓰레드에서 실행**됩니다.

### 2. `@EnableAsync` 설정 방법
`@EnableAsync`는 보통 애플리케이션의 `@Configuration` 클래스에 추가되며, 이로 인해 애플리케이션 전반에서 `@Async`를 사용할 수 있는 환경이 마련됩니다.

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableAsync;

@Configuration
@EnableAsync
public class AppConfig {
    // 여기에 비동기와 관련된 추가 설정을 할 수 있습니다.
}
```

### 3. `@Async` 메서드 사용법
비동기적으로 실행하려는 메서드에 `@Async` 어노테이션을 추가합니다. 그러면 Spring이 해당 메서드를 **비동기적으로 실행하도록 관리**합니다.

```java
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;

@Service
public class AsyncService {

    @Async
    public void performAsyncTask() {
        System.out.println("비동기 작업 시작: " + Thread.currentThread().getName());
        // 긴 작업 수행
    }
}
```

`@Async`를 사용하면 `performAsyncTask` 메서드가 호출 즉시 응답을 반환하고, 해당 작업을 **백그라운드 쓰레드에서 처리**합니다.

### 4. `@EnableAsync`의 상세 동작 원리
- Spring은 `@EnableAsync`를 통해 비동기 작업을 담당할 **기본 Executor(쓰레드 풀)**을 생성합니다.
- 메서드에 `@Async`가 적용되면, Spring은 기본 Executor에서 새로운 쓰레드를 할당해 작업을 수행합니다.
- **기본 Executor**는 **`SimpleAsyncTaskExecutor`**를 사용합니다. 그러나 실무에서는 `ThreadPoolTaskExecutor`를 사용해 쓰레드 풀의 크기를 조정하는 방식으로 성능을 최적화합니다.

### 5. 커스텀 Executor 설정
`@EnableAsync`만 추가한 경우 기본 Executor를 사용하지만, **쓰레드 풀의 크기**나 **쓰레드 이름 설정** 등 **사용자 정의 Executor**가 필요할 경우 `@Bean`을 사용해 `ThreadPoolTaskExecutor`를 정의할 수 있습니다.

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableAsync;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

import java.util.concurrent.Executor;

@Configuration
@EnableAsync
public class AppConfig {

    @Bean(name = "asyncExecutor")
    public Executor asyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);  // 기본 쓰레드 수
        executor.setMaxPoolSize(10);  // 최대 쓰레드 수
        executor.setQueueCapacity(500);  // 큐에 저장될 작업 수
        executor.setThreadNamePrefix("AsyncThread-");
        executor.initialize();
        return executor;
    }
}
```

이렇게 설정한 후, 특정 메서드에 커스텀 Executor를 사용하려면 `@Async("asyncExecutor")`를 사용합니다.

```java
@Async("asyncExecutor")
public void performAsyncTask() {
    // 작업 수행
}
```

### 6. 비동기 작업에서 `SecurityContext`와의 연동
비동기 작업에서 `SecurityContext`가 자동으로 전달되지 않는 경우가 있습니다. Spring Security에서 비동기 작업과 `SecurityContext`를 함께 사용하려면, `MODE_INHERITABLETHREADLOCAL` 설정을 통해 인증 정보를 전달할 수 있습니다. 

### 7. 비동기 예외 처리
비동기 메서드에서 발생한 예외는 호출한 쓰레드에서 직접 처리되지 않습니다. 이를 위해 `AsyncUncaughtExceptionHandler`를 설정하여 비동기 작업 중 발생하는 예외를 처리할 수 있습니다.

```java
import org.springframework.aop.interceptor.AsyncUncaughtExceptionHandler;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.AsyncConfigurer;
import org.springframework.scheduling.annotation.EnableAsync;

import java.lang.reflect.Method;

@Configuration
@EnableAsync
public class AppConfig implements AsyncConfigurer {

    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return (Throwable throwable, Method method, Object... obj) -> {
            System.out.println("Exception message - " + throwable.getMessage());
            System.out.println("Method name - " + method.getName());
        };
    }
}
```

이렇게 하면 비동기 메서드에서 발생한 예외를 지정한 방식으로 처리할 수 있습니다.

### 요약
- `@EnableAsync`는 `@Async`가 사용된 메서드를 별도의 쓰레드에서 실행할 수 있도록 설정하는 어노테이션입니다.
- 기본적으로 `SimpleAsyncTaskExecutor`를 사용하지만, 커스텀 `ThreadPoolTaskExecutor`로 설정을 확장할 수 있습니다.
- 비동기 메서드에서 발생하는 예외 처리를 위해 `AsyncUncaughtExceptionHandler`를 사용할 수 있습니다.
- `SecurityContext` 연동이 필요한 경우 별도의 설정이 필요할 수 있습니다.    