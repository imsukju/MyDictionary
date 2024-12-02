Spring Security가 서블릿 컨테이너의 필터 체인을 프록시로 감싼다는 의미는, **서블릿 컨테이너가 원래 관리하던 필터 체인 앞에 Spring Security가 만든 필터 체인을 중간에 삽입하여 관리한다**는 뜻입니다. 이를 이해하기 위해서는 `FilterChainProxy`가 어떤 역할을 하는지 구체적으로 살펴볼 필요가 있습니다.

### 1. `FilterChainProxy`의 역할

Spring Security는 서블릿 필터의 하나인 `FilterChainProxy`를 사용하여, **Spring Security의 보안 필터 체인을 서블릿 컨테이너의 기본 필터 체인에 연결**합니다. 이 과정에서 Spring Security가 정의한 **여러 보안 필터들**(인증, 권한, CORS, 등)이 서블릿 컨테이너가 요청을 처리하기 전에 실행되도록 하는 겁니다.

### 2. 프록시 역할의 동작 원리
이 프록시 역할이 동작하는 방식은 다음과 같습니다:

1. **서블릿 필터 체인 앞에 Spring Security의 필터 삽입**:
   - Spring Security가 시작될 때, `FilterChainProxy`를 서블릿 컨테이너의 필터 체인에 등록합니다.
   - `FilterChainProxy`는 Spring Security가 만든 `SecurityFilterChain`들을 하나의 필터로 관리하며, 서블릿 컨테이너의 요청 흐름을 제어할 수 있습니다.

2. **HTTP 요청이 들어오면 `FilterChainProxy`가 가장 먼저 실행됨**:
   - 클라이언트에서 HTTP 요청이 들어오면, 서블릿 컨테이너는 등록된 필터 체인에 따라 `FilterChainProxy`부터 실행합니다.
   - `FilterChainProxy`는 Spring Security의 설정에 맞는 `SecurityFilterChain`을 선택하여 해당 요청에 적용합니다.

3. **Spring Security의 보안 필터들이 먼저 실행됨**:
   - 선택된 `SecurityFilterChain`은 Spring Security의 인증, 권한 검사, 세션 관리 등의 보안 필터들로 구성되어 있습니다.
   - `FilterChainProxy`는 서블릿 컨테이너가 요청을 처리하기 전에 이러한 보안 필터들을 먼저 실행합니다.

4. **보안 필터 처리가 끝나면 원래의 서블릿 필터 체인으로 요청을 전달**:
   - Spring Security의 모든 보안 필터가 통과되면, `FilterChainProxy`는 요청을 서블릿 컨테이너의 원래 필터 체인으로 넘겨, 나머지 처리를 하도록 합니다.
   - 이 과정 덕분에, `SecurityFilterChain`에 있는 보안 필터들이 먼저 실행되고, 그 후 서블릿이 요청을 처리하게 됩니다.

이 프록시 메커니즘 덕분에, Spring Security는 서블릿 필터 체인을 직접 수정하지 않고도 HTTP 요청을 제어할 수 있습니다. `FilterChainProxy`가 서블릿 컨테이너의 필터 체인에 포함됨으로써 Spring Security의 모든 보안 설정이 서블릿 컨테이너의 필터 체인 앞단에 연결됩니다.