
### 1. `DisableEncodeUrlFilter`
- **설명**: URL 인코딩을 비활성화하여 URL에 세션 ID가 포함되지 않도록 합니다.
- **옵션**: 특별한 옵션은 제공되지 않으며, URL 세션 ID를 방지하기 위해 단순히 비활성화 처리만 합니다.

### 2. `ForceEagerSessionCreationFilter`
- **설명**: 세션을 즉시 생성하여 세션 관리를 준비합니다. 보안 정책을 강화하기 위해 자주 사용됩니다.
- **옵션**: 특별한 옵션이 없으며, 요청 시작 시 세션 생성 여부를 조절하는 기본 필터로 사용됩니다.

### 3. `ChannelProcessingFilter`
- **설명**: 요청이 HTTP 또는 HTTPS에서 처리되는지 확인하고 보안 채널을 강제하는 필터입니다.
- **옵션**:
  - `requiresSecureChannel`: HTTPS만 허용할 때 true 설정.
  - `requiresInsecureChannel`: HTTP만 허용할 때 true 설정.

### 4. `WebAsyncManagerIntegrationFilter`
- **설명**: 비동기 웹 요청에서 Spring Security 컨텍스트를 관리합니다.
- **옵션**: 비동기 요청용 보안 컨텍스트를 자동 관리하여 별도의 커스터마이징 옵션은 없습니다.

### 5. `SecurityContextHolderFilter`
- **설명**: `SecurityContextHolder`의 인증 컨텍스트를 요청마다 설정하여 인증 정보를 유지합니다.
- **옵션**:
  - `strategyName`: `MODE_THREADLOCAL`(기본값) 또는 `MODE_INHERITABLETHREADLOCAL` 설정 가능.

### 6. `SecurityContextPersistenceFilter`
- **설명**: 이전 요청의 `SecurityContext`를 현재 요청으로 가져와서 세션에서 인증 정보를 유지합니다.
- **옵션**:
  - `forceEagerSessionCreation`: 세션이 없는 경우에도 강제 생성 여부를 결정.

### 7. `HeaderWriterFilter`
- **설명**: 보안 관련 HTTP 응답 헤더를 추가하여 보안성을 강화합니다.
- **옵션**:
  - `X-Content-Type-Options`, `X-XSS-Protection`, `X-Frame-Options`: 각 보안 헤더의 값을 설정할 수 있습니다.

### 8. `CorsFilter`
- **설명**: CORS 요청을 허용하거나 차단합니다.
- **옵션**:
  - `allowedOrigins`, `allowedMethods`, `allowedHeaders`: 허용할 출처, 메서드, 헤더 설정.
  - `allowCredentials`: 쿠키 전송 허용 여부 설정.

### 9. `CsrfFilter`
- **설명**: CSRF 공격 방지를 위해 CSRF 토큰을 관리 및 검증합니다.
- **옵션**:
  - `csrfTokenRepository`: 토큰 저장소 설정.
  - `requireCsrfProtectionMatcher`: 특정 URL에만 CSRF 보호를 적용할 수 있도록 설정.

### 10. `LogoutFilter`
- **설명**: 로그아웃 요청을 처리하여 사용자 세션을 종료하고 인증 정보를 삭제합니다.
- **옵션**:
  - `logoutSuccessUrl`: 로그아웃 후 리다이렉트할 URL 설정.
  - `invalidateHttpSession`: 세션 무효화 여부 설정.

### 11. `OAuth2AuthorizationRequestRedirectFilter`
- **설명**: OAuth2 인증 요청을 처리하고 인증 페이지로 리다이렉트합니다.
- **옵션**:
  - `authorizationRequestRepository`: 인증 요청 저장소 설정.
  - `authorizationUri`: 인증 서버 URI 설정.

### 12. `Saml2WebSsoAuthenticationRequestFilter`
- **설명**: SAML2 SSO 인증 요청을 처리하고 인증 페이지로 리다이렉트합니다.
- **옵션**:
  - `redirectUriTemplate`: 리다이렉트 URI 템플릿 설정.

### 13. `GenerateOneTimeTokenFilter`
- **설명**: 일회성 토큰을 생성하고 이를 검증합니다.
- **옵션**:
  - `tokenRepository`: 토큰 저장소 설정.

### 14. `X509AuthenticationFilter`
- **설명**: X509 인증서로 클라이언트 인증을 수행합니다.
- **옵션**:
  - `principalExtractor`: 인증서에서 주체를 추출하는 로직 설정.
  - `authenticationManager`: 인증 관리자 설정.

### 15. `AbstractPreAuthenticatedProcessingFilter`
- **설명**: 사전 인증된 자격 증명을 사용하여 인증을 처리합니다.
- **옵션**:
  - `preAuthenticatedUserDetailsService`: 사용자 정보 서비스 설정.
  
### 16. `CasAuthenticationFilter`
- **설명**: Central Authentication Service(CAS)를 통해 인증을 처리합니다.
- **옵션**:
  - `casService`: CAS 서버 설정.
  - `ticketValidator`: CAS 티켓 유효성 검사 설정.

### 17. `OAuth2LoginAuthenticationFilter`
- **설명**: OAuth2 로그인 요청을 처리하여 사용자 인증을 수행합니다.
- **옵션**:
  - `authorizationEndpoint`: OAuth2 인증 엔드포인트 설정.
  - `tokenEndpoint`: 토큰 엔드포인트 설정.

### 18. `Saml2WebSsoAuthenticationFilter`
- **설명**: SAML2 SSO 인증 요청을 처리하여 사용자 인증을 수행합니다.
- **옵션**:
  - `samlAuthenticationRequestRepository`: SAML 인증 요청 저장소 설정.

### 19. `UsernamePasswordAuthenticationFilter`
- **설명**: 사용자 이름과 비밀번호로 인증을 수행합니다.
- **옵션**:
  - `usernameParameter`, `passwordParameter`: 요청 파라미터 이름 설정.
  - `authenticationSuccessHandler`, `authenticationFailureHandler`: 성공/실패 시의 핸들러 설정.

### 20. `DefaultResourcesFilter`
- **설명**: 기본 리소스 요청을 처리하여 로그인 페이지 또는 오류 페이지를 제공합니다.
- **옵션**: 특별한 옵션은 없습니다.

### 21. `DefaultLoginPageGeneratingFilter`
- **설명**: 로그인 페이지가 없을 경우 기본 로그인 페이지를 생성합니다.
- **옵션**: 특별한 옵션은 없습니다.

### 22. `DefaultLogoutPageGeneratingFilter`
- **설명**: 로그아웃 페이지가 없을 경우 기본 로그아웃 페이지를 생성합니다.
- **옵션**: 특별한 옵션은 없습니다.

### 23. `DefaultOneTimeTokenSubmitPageGeneratingFilter`
- **설명**: 일회성 토큰 제출 페이지가 없을 경우 자동으로 생성합니다.
- **옵션**: 특별한 옵션은 없습니다.

### 24. `ConcurrentSessionFilter`
- **설명**: 동시 세션 제어를 통해 동일 사용자의 여러 세션 생성을 제한합니다.
- **옵션**:
  - `maximumSessions`: 허용되는 최대 세션 수.
  - `exceptionIfMaximumExceeded`: 최대 세션 초과 시 예외 발생 여부 설정.

### 25. `DigestAuthenticationFilter`
- **설명**: Digest 인증 방식을 사용하여 사용자 인증을 수행합니다.
- **옵션**:
  - `nonceValiditySeconds`: nonce 유효 시간 설정.
  - `userDetailsService`: 사용자 정보 서비스 설정.

### 26. `BearerTokenAuthenticationFilter`
- **설명**: OAuth2 리소스 서버에서 Bearer 토큰을 사용해 사용자 인증을 수행합니다.
- **옵션**:
  - `bearerTokenResolver`: 토큰을 해석하는 로직 설정.

### 27. `BasicAuthenticationFilter`
- **설명**: HTTP 기본 인증 방식으로 사용자 인증을 수행합니다.
- **옵션**: 특별한 옵션이 없으며 기본 HTTP 인증을 제공합니다.

### 28. `AuthenticationFilter`
- **설명**: 사용자 인증을 수행하는 기본 필터입니다.
- **옵션**: 다양한 인증 방법을 설정할 수 있는 필터로, `authenticationManager`, `authenticationConverter` 등의 옵션을 포함합니다.

### 29. `RequestCacheAwareFilter`
- **설명**: 인증되지 않은 상태에서 접근한 URL을 캐시하여, 인증 후 원래 URL로 리다이렉트합니다.
- **옵션**:
  - `requestCache`: 요청 캐시 저장소 설정.

### 30. `SecurityContextHolderAwareRequestFilter`
- **설명**: 요청에 사용자 인증 정보를 통합하여 사용할 수 있도록 합니다.
- **옵션**: 특별한 옵션은 없으며 인증 정보의 활용성을 높입니다.

### 31. `JaasApiIntegrationFilter`
- **설명**: JAAS(Java Authentication and Authorization Service)를 통해 인증을 수행합니다.
- **옵션**: JAAS 관련 설정이 필요한 경우 사용됩니다.

### 32. `RememberMeAuthenticationFilter`
- **설명**: Remember-Me 기능을 통해 세션 만료 후에도 사용자 인증을 유지합니다.
- **옵션**:
  - `rememberMeServices`: Remember-Me 서비스 설정.

### 33. `AnonymousAuthenticationFilter`
- **설명**: 인증되지 않은 사용자를 익명 사용자로 처리하여 접근을 허용하거나 차단합니다.
- **옵션**:
  - `

key`: 익명 인증에 사용할 고유 키 설정.

### 34. `OAuth2AuthorizationCodeGrantFilter`
- **설명**: OAuth2의 Authorization Code Grant 방식으로 인증을 처리합니다.
- **옵션**: OAuth2 인증 관련 URI와 토큰을 설정합니다.

### 35. `SessionManagementFilter`
- **설명**: 세션 관리를 담당하며, 세션 고정 공격을 방지하기 위해 세션을 재생성할 수 있습니다.
- **옵션**:
  - `sessionAuthenticationStrategy`: 세션 관리 전략 설정.

### 36. `ExceptionTranslationFilter`
- **설명**: 인증/권한 예외 발생 시 이를 처리하고 오류 페이지로 리다이렉트합니다.
- **옵션**:
  - `accessDeniedHandler`, `authenticationEntryPoint`: 예외 처리 핸들러 설정.

### 37. `FilterSecurityInterceptor`
- **설명**: URL 접근 제어를 위해 사용되며, 사용자가 접근할 수 있는 리소스를 정의합니다.
- **옵션**:
  - `accessDecisionManager`: 접근 결정 관리 설정.
  - `securityMetadataSource`: 리소스 메타데이터 소스 설정.

### 38. `AuthorizationFilter`
- **설명**: 특정 리소스에 대한 접근 권한을 확인하여 허용 여부를 결정합니다.
- **옵션**: 특별한 옵션 없이 간단한 접근 권한 검사를 수행합니다.

### 39. `SwitchUserFilter`
- **설명**: 관리자가 다른 사용자의 세션으로 전환할 수 있도록 지원하는 필터입니다.
- **옵션**:
  - `switchUserAuthorityRole`: 관리자 전환 시 부여할 역할 설정.