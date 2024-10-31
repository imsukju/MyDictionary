`OncePerRequestFilter`는 Spring Framework에서 제공하는 `javax.servlet.Filter`의 확장 클래스로, 이름 그대로 한 HTTP 요청마다 한 번씩 실행되는 필터입니다. 이 필터는 Spring Security를 비롯해 다양한 경우에서 HTTP 요청의 전처리, 후처리 작업을 하는 데에 사용됩니다. 이제 기초부터 자세히 설명드리겠습니다.

### 1. `OncePerRequestFilter`의 개념
`OncePerRequestFilter`는 HTTP 요청을 처리하는 과정에서 요청이 필터를 여러 번 거치지 않도록 보장합니다. 특정 URL 패턴에 대해 요청이 여러 번 필터를 거치면 중복 처리될 수 있는 상황을 방지하며, 요청당 한 번만 실행되도록 하여 효율적으로 요청을 필터링합니다.

#### 예시
일반적인 `Filter`는 요청이 다양한 경로로 들어오면 중복 실행될 수 있는 문제가 있는데, `OncePerRequestFilter`는 이러한 문제를 방지합니다. 예를 들어 인증 정보를 확인하는 필터가 있을 때, 요청마다 인증이 한 번씩만 검증되도록 설정할 수 있습니다.

### 2. `OncePerRequestFilter`의 사용 목적
이 필터는 다음과 같은 목적에서 주로 사용됩니다.

- **보안 처리**: 요청마다 인증 또는 권한 검사를 수행.
- **로그 기록**: 요청마다 로그를 기록하는 데 사용.
- **데이터 전처리**: 요청을 필터링하고 필요한 데이터를 추출 또는 수정.
- **CORS 처리**: 요청이 특정 도메인에서 발생했는지 확인하여 허용하거나 차단.

### 3. 기본 구조
`OncePerRequestFilter`를 구현할 때는 `doFilterInternal` 메서드를 오버라이드합니다. 이 메서드는 실제 필터 로직을 정의하며, Spring이 각 요청마다 이 메서드를 호출합니다.

```java
public class CustomFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain)
                                    throws ServletException, IOException {
        // 필터 로직 작성
        filterChain.doFilter(request, response); // 다음 필터로 전달
    }
}
```

위 코드에서 `doFilterInternal` 메서드는 `HttpServletRequest`, `HttpServletResponse`, `FilterChain`을 매개변수로 받습니다. 여기서 `filterChain.doFilter(request, response);`는 다음 필터로 요청을 전달하는 역할을 합니다. 이 메서드를 호출하지 않으면 요청 처리가 중단됩니다.

### 4. 동작 방식
1. **요청이 들어옴**: 클라이언트가 서버에 요청을 보내면 Spring의 DispatcherServlet이 요청을 처리합니다.
2. **필터 체인에서 `OncePerRequestFilter` 실행**: 등록된 필터 중 `OncePerRequestFilter`가 있으면 각 요청마다 한 번만 실행됩니다.
3. **`doFilterInternal` 메서드 실행**: `doFilterInternal` 메서드 안에 정의된 로직이 실행됩니다.
4. **다음 필터로 전달**: `filterChain.doFilter(request, response);` 메서드를 통해 다음 필터로 요청이 전달됩니다. 모든 필터가 끝나면 컨트롤러로 요청이 넘어가고, 응답이 돌아오며 거꾸로 필터 체인을 거칩니다.

### 5. 예제: JWT 인증 필터
`OncePerRequestFilter`를 사용해 JWT 토큰 인증을 구현할 수 있습니다. 요청마다 한 번씩 토큰을 검사하여 인증이 필요한 요청인지 확인합니다.

```java
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    private final JwtTokenProvider jwtTokenProvider;

    public JwtAuthenticationFilter(JwtTokenProvider jwtTokenProvider) {
        this.jwtTokenProvider = jwtTokenProvider;
    }

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain)
                                    throws ServletException, IOException {
        String token = jwtTokenProvider.resolveToken(request);

        if (token != null && jwtTokenProvider.validateToken(token)) {
            Authentication auth = jwtTokenProvider.getAuthentication(token);
            SecurityContextHolder.getContext().setAuthentication(auth);
        }

        filterChain.doFilter(request, response);
    }
}
```

1. **토큰 추출**: `resolveToken` 메서드로 요청에서 토큰을 추출합니다.
2. **토큰 유효성 검사**: `validateToken` 메서드로 토큰의 유효성을 확인합니다.
3. **인증 정보 설정**: 토큰이 유효한 경우 인증 정보를 `SecurityContextHolder`에 설정하여 요청이 인증된 상태로 처리됩니다.

### 6. `OncePerRequestFilter`의 장점
- **단일 요청 처리**: 요청 당 한 번만 필터가 실행되므로 중복 처리를 방지합니다.
- **편리한 확장성**: 다양한 전처리, 후처리 작업을 커스터마이징할 수 있습니다.
- **Spring Security와의 통합**: 인증/인가 로직에서 자주 사용되며, 손쉽게 인증 정보를 관리할 수 있습니다.

### 7. 적용 방법
`OncePerRequestFilter`를 사용하려면 Spring Security 설정에서 필터를 등록하거나 `FilterRegistrationBean`을 통해 직접 등록할 수 있습니다.

```java
@Bean
public FilterRegistrationBean<JwtAuthenticationFilter> jwtAuthenticationFilter() {
    FilterRegistrationBean<JwtAuthenticationFilter> registrationBean = new FilterRegistrationBean<>();
    registrationBean.setFilter(new JwtAuthenticationFilter(jwtTokenProvider));
    registrationBean.addUrlPatterns("/api/*"); // 특정 URL 패턴에 적용
    return registrationBean;
}
```

이렇게 설정하면 `JwtAuthenticationFilter`가 `/api/*` 경로의 모든 요청에 대해 한 번씩 실행됩니다.

### 8. 결론
`OncePerRequestFilter`는 Spring 환경에서 HTTP 요청을 한 번만 필터링하여 전처리 또는 후처리 로직을 구현하기에 유용한 클래스입니다. 주로 보안과 관련된 작업에서 많이 사용되며, 요청을 효율적으로 관리하는 데 큰 장점을 제공합니다. Spring Security와 잘 통합되며 다양한 커스터마이징이 가능하여 많은 개발자가 사용하는 유용한 필터입니다.