![SpringSecurity Context Holder](./Reesource/securitycontextholder.png)
> 출처 https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html
`SecurityContext`는 Spring Security에서 **현재 사용자의 인증 상태와 보안 정보를 저장**하고 관리하는 객체입니다. `SecurityContext`는 Spring Security 애플리케이션 전반에서 **현재 인증된 사용자에 대한 정보를 중앙에서 조회할 수 있도록** 설계된 구조의 핵심 요소입니다. 이를 이해하려면 `SecurityContext`의 역할, 구성 요소, 동작 방식을 단계별로 자세히 살펴보겠습니다.

### 1. `SecurityContext`의 역할

`SecurityContext`는 주로 다음과 같은 역할을 수행합니다:

- **인증 정보 저장**: 사용자가 애플리케이션에 접근할 때, `SecurityContext`는 로그인한 사용자의 **인증 정보와 권한**을 포함하는 `Authentication` 객체를 저장합니다.
- **애플리케이션 전역에서 인증 정보 제공**: Spring Security는 `SecurityContextHolder`를 통해 `SecurityContext`를 관리하는데, 이로 인해 애플리케이션 어디서나 현재 사용자의 인증 정보를 일관되게 조회할 수 있습니다.
- **사용자 권한 검증**: `SecurityContext`에 저장된 인증 정보를 바탕으로 사용자에게 허용된 접근 권한을 결정합니다. 

이러한 역할 덕분에 `SecurityContext`는 각 요청이 안전하게 인증 및 권한 확인을 할 수 있도록 중앙에서 역할을 수행합니다.

### 2. `SecurityContext`의 구성 요소: `Authentication` 객체

`SecurityContext`의 가장 중요한 구성 요소는 **`Authentication` 객체**입니다. 이 객체는 **인증된 사용자의 세부 정보를 담고 있으며**, 주로 아래의 정보들을 포함합니다.

- **Principal**: 주로 인증된 사용자의 이름 또는 ID를 나타내며, `UserDetails` 인터페이스의 구현체가 이 필드에 할당됩니다.
- **Credentials**: 사용자가 인증할 때 사용한 자격 증명(예: 비밀번호)입니다. 하지만 보안을 위해, 인증이 완료되면 이 정보는 `null`로 설정하는 것이 일반적입니다.
- **Authorities**: 현재 사용자가 가진 **권한 목록**입니다. 이 권한 목록은 `ROLE_ADMIN`, `ROLE_USER`와 같이 사용자의 권한을 지정하는 데 사용됩니다.
- **Details**: 사용자에 대한 추가 정보를 담고 있습니다. 예를 들어, 인증이 이루어진 소스 IP 주소나 세션 ID 등이 포함될 수 있습니다.
- **Authenticated**: 사용자가 **인증 상태**인지 아닌지를 나타내는 Boolean 값입니다. 인증이 완료된 경우 `true`로 설정됩니다.

### 3. `SecurityContext`의 생성 및 설정 과정

`SecurityContext`는 보통 사용자가 애플리케이션에 **로그인**하면 생성됩니다. 이 과정은 크게 다음과 같은 단계로 이루어집니다.

1. **사용자 인증 요청**: 사용자가 아이디와 비밀번호 등 자격 증명을 입력하면 Spring Security는 `AuthenticationManager`를 통해 이를 검증합니다.
   
2. **Authentication 객체 생성**: 사용자가 올바르게 인증되면, Spring Security는 `Authentication` 객체를 생성하고, 사용자의 ID, 권한, 인증 상태 등을 설정합니다.

3. **SecurityContext에 Authentication 설정**: 생성된 `Authentication` 객체는 `SecurityContext`에 저장되며, 이후 이 `SecurityContext`는 `SecurityContextHolder`에 설정됩니다.

4. **HTTP 세션과 연동**: `SecurityContext`는 `SecurityContextHolder`를 통해 애플리케이션 전역에서 접근할 수 있으며, 웹 애플리케이션의 경우 **HTTP 세션**에 저장되어 세션 간 일관성을 유지할 수 있습니다.

### 4. `SecurityContext`와 `SecurityContextHolder`의 관계

`SecurityContext`는 `SecurityContextHolder`를 통해 관리됩니다. `SecurityContextHolder`는 각 스레드마다 고유한 `SecurityContext`를 제공하여, 애플리케이션이 멀티스레드 환경에서도 안전하게 인증 정보를 유지할 수 있도록 합니다. 

이를 위해 `SecurityContextHolder`는 기본적으로 **스레드 로컬 저장소**를 사용합니다. 예를 들어, 한 요청의 인증 정보를 다른 요청이 참조하지 않도록, 각 스레드가 자신의 `SecurityContext`를 독립적으로 가질 수 있도록 설계된 것입니다.

### 5. HTTP 세션에서의 `SecurityContext` 유지

웹 애플리케이션에서는 `SecurityContext`가 **HTTP 세션과 연결**되어 유지됩니다. 즉, 사용자가 로그인 후 요청을 할 때마다 `SecurityContext`는 HTTP 세션을 통해 일관된 인증 정보를 제공합니다. Spring Security의 `SessionManagementFilter`가 HTTP 세션에서 `SecurityContext`를 자동으로 가져와, **요청마다 동일한 인증 상태를 유지**하도록 해줍니다.

1. **로그인 시**: 사용자가 인증을 마치면 `SecurityContext`는 `SecurityContextHolder`에 설정되며, HTTP 세션에 함께 저장됩니다.
   
2. **새로운 요청 시**: 새로운 요청이 들어올 때마다 Spring Security는 HTTP 세션에서 `SecurityContext`를 가져와 `SecurityContextHolder`에 설정합니다.

3. **로그아웃 시**: 사용자가 로그아웃할 때 HTTP 세션에 저장된 `SecurityContext`가 삭제됩니다. 이로 인해 이후의 요청에서는 인증 정보가 유지되지 않습니다.

### 6. `SecurityContext`의 주의사항

`SecurityContext`는 Spring Security에서 핵심적인 역할을 수행하지만, 사용자가 직접 조작하거나 임의로 설정할 경우 보안 위험이 생길 수 있습니다. 아래와 같은 사항을 고려하는 것이 좋습니다:

- **직접 설정 및 제거**: 애플리케이션 코드에서 `SecurityContext`를 직접 설정하거나 제거할 때는 주의해야 합니다. 인증된 사용자가 바뀌는 상황에서는 세션이나 스레드 로컬 저장소에 존재하는 `SecurityContext`를 적절히 관리해야 합니다.
  
- **동시성 문제**: 특히 다중 스레드 환경에서 `SecurityContext`는 스레드마다 독립적으로 유지되어야 합니다. 이를 위해 `SecurityContextHolder`의 전략을 잘 설정해야 하며, 스레드 풀에서 `SecurityContext`가 올바르게 복사되도록 `DelegatingSecurityContextRunnable` 등의 보조 클래스를 사용할 수 있습니다.

### 요약

- **`SecurityContext`**: Spring Security에서 현재 사용자의 인증 정보를 저장하는 핵심 객체입니다.
- **Authentication 객체 포함**: `SecurityContext`는 사용자의 인증 상태, 권한, ID 등을 포함하는 `Authentication` 객체를 포함합니다.
- **`SecurityContextHolder`를 통해 관리**: `SecurityContextHolder`는 각 스레드마다 고유의 `SecurityContext`를 관리하여 멀티스레드 환경에서의 인증 정보 보호를 돕습니다.
- **HTTP 세션과 연동 가능**: HTTP 세션과 연동되어, 로그인 상태를 요청 간에 일관성 있게 유지합니다.

이러한 구조 덕분에 Spring Security는 **애플리케이션 전역에서 일관된 인증 상태를 유지하며 보안 관리**를 가능하게 합니다.