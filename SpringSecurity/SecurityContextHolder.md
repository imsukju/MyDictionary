![SpringSecurity Context Holder](./Reesource/securitycontextholder.png) 
>출처 https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html
`SecurityContextHolder`는 Spring Security에서 **현재 스레드의 인증 상태를 저장**하고 **관리하는 핵심 클래스**입니다. 이 클래스는 주로 현재 애플리케이션에서 실행되는 스레드가 "누구인지" 알 수 있도록 하며, 각 스레드마다 **독립적인 인증 정보**를 유지하도록 설계되었습니다. 이를 이해하기 위해 Spring Security에서 `SecurityContextHolder`가 어떻게 역할을 하고 어떻게 쓰레드 로컬 저장소(Thread-local Storage)를 사용하는지 단계별로 설명드리겠습니다.

### 1. `SecurityContextHolder`의 역할과 배경

`SecurityContextHolder`는 **애플리케이션의 전반적인 보안 맥락을 제공하는 핵심 클래스**입니다. 애플리케이션의 어느 위치에서든 현재 사용자의 인증 상태를 조회할 수 있도록 중앙 관리 역할을 수행합니다. 

Spring Security는 웹 애플리케이션에서 사용자 인증 정보를 저장하고 이 정보에 따라 접근 권한을 제어하는 기능을 제공합니다. 인증 정보는 **로그인한 사용자의 ID, 역할, 권한** 등을 포함하고 있으며, 이러한 정보는 현재 **어떤 사용자가 시스템에 접근하고 있는지 확인하는 데 필요**합니다.

### 2. `SecurityContext`와 `Authentication`

`SecurityContextHolder`는 각 스레드마다 고유한 **`SecurityContext` 객체**를 저장하는 데 이 `SecurityContext`는 **현재 인증된 사용자의 정보를 담고 있는 객체**입니다. `SecurityContext`는 `Authentication` 객체를 포함하며, 이 `Authentication` 객체는 인증된 사용자의 세부 정보를 담고 있습니다.

- **`SecurityContext`**: 현재 스레드의 인증 상태를 저장하며, `SecurityContextHolder`를 통해 접근합니다.
- **`Authentication`**: 인증된 사용자의 ID, 권한, 인증 방식 등을 포함하는 객체입니다.

일반적으로 인증이 완료되면 `SecurityContext`에 `Authentication` 객체가 설정되며, 이를 통해 애플리케이션의 다른 부분에서도 이 인증 정보를 사용할 수 있게 됩니다.

### 3. `SecurityContextHolder`의 작동 방식: 쓰레드 로컬 저장소

`SecurityContextHolder`는 **쓰레드 로컬 저장소**를 사용하여 각 스레드마다 독립적인 `SecurityContext`를 유지합니다. 쓰레드 로컬 저장소는 **각각의 스레드가 자신만의 공간을 가지도록** 하여, 서로 다른 스레드 간의 데이터를 격리하고 보호할 수 있는 메커니즘을 제공합니다. 이 방식은 아래와 같은 원리로 동작합니다:

- **쓰레드가 생성될 때**: `SecurityContextHolder`는 이 쓰레드 전용의 `SecurityContext`를 생성하고 저장합니다.
- **스레드가 실행 중일 때**: `SecurityContextHolder`는 각 스레드가 자신만의 `SecurityContext`에 접근하여 인증 정보를 안전하게 조회할 수 있게 합니다.
- **쓰레드가 종료될 때**: `SecurityContextHolder`는 해당 스레드에 저장된 `SecurityContext`를 제거하여 인증 정보를 폐기하고, 다른 스레드와의 충돌을 방지합니다.

이러한 스레드 로컬 저장소 방식 덕분에 여러 스레드가 동시에 동작하는 상황에서도 각 스레드는 자신의 인증 정보를 별도로 관리할 수 있게 됩니다.

### 4. 쓰레드 풀 환경에서의 `SecurityContext` 복사

웹 애플리케이션에서 **멀티스레딩 환경**이 필수적일 때, 특히 **스레드 풀**을 사용할 경우 `SecurityContext`를 다른 스레드로 안전하게 전달하는 것이 중요합니다. 스레드 풀에서는 동일한 스레드가 여러 요청을 처리할 수 있기 때문에, `SecurityContextHolder`의 `SecurityContext`를 적절히 관리해야 합니다. Spring Security는 **DelegatingSecurityContextRunnable** 및 **DelegatingSecurityContextCallable** 클래스를 통해 이를 해결합니다.

- **DelegatingSecurityContextRunnable**: Runnable 인터페이스를 구현한 클래스입니다. 스레드 풀에서 `SecurityContext`를 복사하여 사용해야 할 때, 이를 감싸고 스레드가 실행될 때마다 해당 `SecurityContext`를 현재 스레드에 적용하도록 합니다.
- **DelegatingSecurityContextCallable**: Callable 인터페이스를 구현한 클래스입니다. DelegatingSecurityContextRunnable과 동일한 역할을 하지만, 반환값이 필요한 경우에 사용됩니다.

### 5. HTTP 세션과 `SecurityContextHolder`

웹 애플리케이션에서 `SecurityContext`는 **HTTP 세션**에 저장되어 세션 간에도 인증 정보를 공유하도록 설정할 수 있습니다. 이는 사용자가 새로운 요청을 보낼 때마다 `SecurityContext`를 유지하게 도와주는 기능입니다.

1. **로그인 시**: Spring Security는 인증이 완료되면 `SecurityContext`를 `SecurityContextHolder`에 설정하고, 이 객체를 HTTP 세션에 저장합니다.
2. **새로운 요청 시**: 새로운 요청이 들어오면, Spring Security는 HTTP 세션에서 `SecurityContext`를 복원하여 `SecurityContextHolder`에 설정합니다.
3. **로그아웃 시**: 로그아웃이 발생하면 HTTP 세션에 저장된 `SecurityContext`가 삭제됩니다.

이로써 사용자가 로그인을 유지하는 동안 동일한 `SecurityContext`를 HTTP 세션을 통해 지속적으로 사용할 수 있게 됩니다.

### 6. `SecurityContextHolder`의 전역 접근 방식

애플리케이션 어디에서나 `SecurityContextHolder`를 사용해 `SecurityContext`에 접근할 수 있습니다. 예를 들어, 현재 사용자의 인증 정보를 얻기 위해 다음과 같은 코드를 사용할 수 있습니다.

```java
Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
if (authentication != null && authentication.isAuthenticated()) {
    System.out.println("Authenticated user: " + authentication.getName());
}
```

이 방식은 Spring Security가 **애플리케이션 전역에서 동일한 인증 정보를 일관되게** 사용할 수 있게 해 줍니다.

### 7. `SecurityContextHolder`의 전략 설정

`SecurityContextHolder`는 기본적으로 **ThreadLocal 전략**을 사용하지만, 상황에 따라 **SecurityContext를 관리하는 전략**을 변경할 수 있습니다. 이를 위해 `SecurityContextHolder`는 다음의 세 가지 전략을 제공합니다.

- **MODE_THREADLOCAL**: 기본 전략으로, 각 스레드가 독립적으로 `SecurityContext`를 가지게 합니다.
- **MODE_INHERITABLETHREADLOCAL**: 부모 스레드에서 생성된 `SecurityContext`를 자식 스레드에서도 사용할 수 있게 합니다.
- **MODE_GLOBAL**: 애플리케이션 전역에서 단일 `SecurityContext`를 공유하도록 설정합니다.

이 전략들은 `SecurityContextHolder.setStrategyName()` 메서드를 사용하여 설정할 수 있습니다.

```java
SecurityContextHolder.setStrategyName(SecurityContextHolder.MODE_INHERITABLETHREADLOCAL);
```

각 전략은 다른 쓰레드 간의 인증 정보 공유를 어떻게 관리할지에 대한 방식을 달리하여, 상황에 맞게 유연하게 사용할 수 있습니다.

### 요약

- **SecurityContextHolder**: Spring Security의 중심 클래스이며, 애플리케이션에서 현재 사용자의 인증 정보를 관리합니다.
- **SecurityContext**: 현재 스레드의 인증 상태를 저장하는 객체이며, `Authentication` 객체를 포함합니다.
- **Thread-local Storage**: 각 스레드마다 고유의 `SecurityContext`를 관리하여 안전한 인증을 보장합니다.
- **HTTP 세션과 연동**: 세션을 통해 `SecurityContext`를 지속적으로 사용할 수 있습니다.
- **전략 설정**: ThreadLocal을 포함한 다양한 전략을 통해 상황에 맞게 `SecurityContext` 관리 방식을 선택할 수 있습니다.

이러한 구조와 원리를 통해 `SecurityContextHolder`는 Spring Security에서 **강력하고 안전한 인증 관리**를 가능하게 합니다.