Spring Security에서 `AuthenticationProvider`, `AuthenticationManager`, 그리고 `AuthenticationFilter`는 인증 흐름을 책임지는 핵심 요소들입니다. 이 요소들이 어떻게 유기적으로 작동하여 인증 시스템을 구축하는지 자세히 살펴보겠습니다.

---

## 1. AuthenticationProvider

**`AuthenticationProvider`**는 특정 인증 방식을 정의하는 역할을 합니다. 다양한 인증 소스나 방식을 지원할 수 있도록 설계되었습니다. Spring Security에서는 여러 `AuthenticationProvider`를 등록할 수 있고, 각 프로바이더가 특정 인증 방식을 담당하게 됩니다.

### 역할
- `AuthenticationProvider`는 **특정 인증 방식**을 구현하고 이를 통해 사용자가 유효한지를 확인합니다.
- 예를 들어, 하나의 `AuthenticationProvider`는 **데이터베이스**에 저장된 사용자 정보를 기반으로 인증을 수행하고, 또 다른 `AuthenticationProvider`는 **LDAP** 같은 외부 시스템을 통해 인증을 수행할 수 있습니다.

### 주요 메서드
- **authenticate(Authentication authentication)**: 이 메서드는 사용자의 인증을 수행하는 핵심 메서드입니다. `Authentication` 객체를 받아 유효성을 검사하고, 인증에 성공하면 새로운 `Authentication` 객체를 반환합니다. 인증 실패 시에는 예외를 던집니다.
- **supports(Class<?> authentication)**: 이 메서드는 `AuthenticationProvider`가 지원하는 인증 유형을 결정합니다. 예를 들어, `UsernamePasswordAuthenticationToken` 타입만을 인증하도록 지정할 수 있습니다.

### 예제
```java
@Component
public class CustomAuthenticationProvider implements AuthenticationProvider {
    private final UserDetailsService userDetailsService;
    private final PasswordEncoder passwordEncoder;

    public CustomAuthenticationProvider(UserDetailsService userDetailsService, PasswordEncoder passwordEncoder) {
        this.userDetailsService = userDetailsService;
        this.passwordEncoder = passwordEncoder;
    }

    @Override
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
        String username = authentication.getName();
        String password = authentication.getCredentials().toString();

        UserDetails user = userDetailsService.loadUserByUsername(username);

        if (passwordEncoder.matches(password, user.getPassword())) {
            return new UsernamePasswordAuthenticationToken(username, password, user.getAuthorities());
        } else {
            throw new BadCredentialsException("Invalid username or password");
        }
    }

    @Override
    public boolean supports(Class<?> authentication) {
        return UsernamePasswordAuthenticationToken.class.isAssignableFrom(authentication);
    }
}
```

위 예제에서 `CustomAuthenticationProvider`는 `UsernamePasswordAuthenticationToken`을 지원하고, 사용자 이름과 비밀번호를 기반으로 인증을 수행합니다.

---

## 2. AuthenticationManager

**`AuthenticationManager`**는 인증 요청을 `AuthenticationProvider`에 위임하여 전체 인증 프로세스를 관리하는 핵심 컴포넌트입니다. 단일 또는 다수의 `AuthenticationProvider`를 연결해 일련의 인증 절차를 처리하는 데 유용합니다. `AuthenticationManager`는 일반적으로 여러 인증 방법을 지원할 수 있게 합니다.

### 역할
- 여러 `AuthenticationProvider`를 설정할 수 있어 다양한 인증 방식을 사용할 수 있습니다.
- `AuthenticationManager`는 `authenticate()` 메서드를 통해 인증 요청을 수신하고, 각 `AuthenticationProvider`에 요청을 전달하여 인증을 시도합니다. 어느 한 `AuthenticationProvider`라도 인증에 성공하면 인증이 완료됩니다.

### 주요 구현체: `ProviderManager`
- `ProviderManager`는 `AuthenticationManager`의 주요 구현체로, 여러 `AuthenticationProvider`를 체인으로 연결하여 각 `AuthenticationProvider`에 순차적으로 인증을 위임합니다.
- 첫 번째로 인증을 성공하는 `AuthenticationProvider`가 결과를 반환하며, 모두 실패할 경우 예외가 발생합니다.

### 예제
```java
AuthenticationProvider provider1 = new CustomAuthenticationProvider();
AuthenticationProvider provider2 = new AnotherAuthenticationProvider();

AuthenticationManager manager = new ProviderManager(Arrays.asList(provider1, provider2));

Authentication authentication = manager.authenticate(new UsernamePasswordAuthenticationToken("user", "password"));
```

이 예제에서 `ProviderManager`는 `provider1`과 `provider2`를 체인으로 연결해 `UsernamePasswordAuthenticationToken` 타입의 인증 요청을 처리합니다.

---

## 3. AuthenticationFilter

**`AuthenticationFilter`**는 HTTP 요청을 가로채어 인증 정보를 추출하고, 이를 `AuthenticationManager`에 전달하는 역할을 합니다. `AuthenticationFilter`는 HTTP 요청의 특정한 헤더나 파라미터에서 인증 정보를 가져와 로그인 요청을 처리하는 필터입니다.

### 역할
- 사용자가 서버에 인증 요청을 보낼 때, **HTTP 요청**에서 인증 정보를 추출하는 역할을 수행합니다. 예를 들어, 사용자 이름과 비밀번호를 `POST` 폼으로부터 추출하거나, 헤더에 담긴 JWT 토큰을 읽어들일 수 있습니다.
- 그런 다음 추출한 인증 정보를 `AuthenticationManager`에게 전달하여 인증을 처리합니다.

### 주요 구현체
- **UsernamePasswordAuthenticationFilter**: 기본 폼 로그인 방식으로 사용자 이름과 비밀번호를 인증할 때 사용하는 필터입니다.
- **BearerTokenAuthenticationFilter**: `Bearer` 토큰을 사용하는 경우, 주로 JWT 방식의 인증에서 사용되는 필터입니다.
- **BasicAuthenticationFilter**: HTTP Basic 인증을 처리하는 필터입니다.

### 동작 원리
1. 사용자가 `/login`과 같은 엔드포인트로 로그인 요청을 보냅니다.
2. `AuthenticationFilter`는 요청의 **본문**, **헤더**, **쿠키** 등에서 인증 정보를 추출합니다.
3. 추출한 인증 정보를 `AuthenticationManager`에 전달하여 인증을 수행합니다.
4. 인증에 성공하면 `SecurityContext`에 인증 정보를 저장하고, 다음 필터나 컨트롤러로 요청을 전달합니다.

### 예제
```java
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    private final AuthenticationManager authenticationManager;

    public JwtAuthenticationFilter(AuthenticationManager authenticationManager) {
        this.authenticationManager = authenticationManager;
    }

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        String token = request.getHeader("Authorization");

        if (token != null && token.startsWith("Bearer ")) {
            token = token.substring(7);
            Authentication authRequest = new JwtAuthenticationToken(token);
            Authentication authResult = authenticationManager.authenticate(authRequest);
            SecurityContextHolder.getContext().setAuthentication(authResult);
        }

        filterChain.doFilter(request, response);
    }
}
```

이 `JwtAuthenticationFilter`는 `Authorization` 헤더에서 JWT 토큰을 추출하고, `JwtAuthenticationToken`을 생성하여 `AuthenticationManager`에 전달합니다. 성공 시 `SecurityContext`에 인증 정보를 설정합니다.

---

### 요약 비교

| 요소                      | 역할                                                                                       | 주요 메서드                                    | 주요 사용 시기 및 대상       |
|-------------------------|------------------------------------------------------------------------------------------|----------------------------------------------|-----------------------------|
| **AuthenticationProvider** | 개별 인증 방식 구현 (ex. 데이터베이스, LDAP, OAuth 등)                                            | `authenticate()`, `supports()`               | 인증 방식을 추가할 때 사용   |
| **AuthenticationManager** | 여러 `AuthenticationProvider`를 묶어 인증 프로세스를 관리                                       | `authenticate()`                              | 모든 인증 요청의 진입점      |
| **AuthenticationFilter**  | HTTP 요청에서 인증 정보를 추출하여 `AuthenticationManager`에 전달                              | `doFilterInternal()`                          | HTTP 요청의 인증 처리 시 사용 |

이 세 가지가 유기적으로 협력하여 사용자 인증을 처리하는 Spring Security의 핵심 인증 흐름을 구성합니다.