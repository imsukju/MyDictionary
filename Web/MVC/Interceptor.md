Spring MVC에서 **Interceptor**는 HTTP 요청과 응답에 대해 로직을 추가하거나 변경할 수 있는 강력한 기능을 제공합니다. 이 기능을 통해 컨트롤러로 요청이 전달되기 전에 요청을 가로채거나, 컨트롤러가 요청을 처리한 후 응답을 수정할 수 있습니다. **Interceptor**는 주로 **로깅, 감사(auditing)**, **요청/응답 객체 수정** 등과 같은 기능을 구현할 때 유용하게 사용됩니다.

### Interceptor의 작동 방식

Interceptor는 **Servlet Filter**와 유사하게 동작하지만, **Spring Web Application Context**와 더 긴밀하게 통합됩니다. Interceptor는 **전역적으로** 모든 요청에 적용되거나 **특정 URL 패턴**에만 적용될 수 있습니다.

#### 주요 메서드:
1. **Pre-Handle (`preHandle`)**: 컨트롤러의 메서드가 호출되기 전에 실행됩니다. 이 메서드가 `true`를 반환하면 요청은 컨트롤러로 계속 전달됩니다. `false`를 반환하면 요청 처리가 중단되고, 즉시 응답이 반환됩니다.
2. **Post-Handle (`postHandle`)**: 컨트롤러 메서드가 실행된 후, 하지만 **뷰가 렌더링되기 전**에 호출됩니다. 
3. **After-Completion (`afterCompletion`)**: **뷰가 렌더링된 후**, 요청 처리가 완료된 시점에 호출됩니다.

### Java Configuration에서 Interceptor 등록

Java 기반 구성에서 **Interceptor**는 `WebMvcConfigurer` 인터페이스를 구현한 후, `addInterceptors()` 메서드를 오버라이드하여 등록할 수 있습니다.

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // LocaleChangeInterceptor를 추가하여 각 요청에서 locale을 변경할 수 있게 함
        registry.addInterceptor(new LocaleChangeInterceptor());

        // ThemeChangeInterceptor를 추가하되, /admin 경로는 제외
        registry.addInterceptor(new ThemeChangeInterceptor())
                .addPathPatterns("/**")  // 모든 경로에 적용
                .excludePathPatterns("/admin/**");  // /admin 경로는 제외
    }
}
```

#### 주요 구성 요소:
- `registry.addInterceptor()`: Interceptor를 등록합니다.
- `.addPathPatterns("/**")`: 모든 경로에 Interceptor를 적용합니다.
- `.excludePathPatterns("/admin/**")`: 특정 경로를 Interceptor에서 제외합니다.

위 예시에서 `LocaleChangeInterceptor`는 요청 파라미터를 기반으로 애플리케이션의 **locale**을 동적으로 변경할 수 있게 하고, `ThemeChangeInterceptor`는 **테마**를 동적으로 변경할 수 있게 합니다.

### Interceptor의 보안 한계

Spring MVC의 Interceptor는 **보안 목적**으로는 사용되지 않는 것이 좋습니다. Interceptor는 요청이 **HandlerMapping**에 매핑된 후 실행되므로, **보안 계층**에서는 충분히 일관성 있게 동작하지 않을 수 있습니다. 대신, 보안 작업은 요청 처리 초기 단계에서 실행되는 **Servlet Filter** 또는 **Spring Security**를 사용하는 것이 더 적합합니다. 이러한 방법은 요청이 애플리케이션에 도달하기 전에 처리되어야 하는 **인증** 및 **인가** 작업을 안전하게 수행할 수 있습니다.

### Interceptor의 XML Configuration

XML 기반 Spring 구성에서는 **MappedInterceptor**를 선언하여 모든 `HandlerMapping` 빈에서 자동으로 감지되도록 할 수 있습니다. 이는 Java Configuration에서의 Interceptor 등록 방식과는 차이가 있습니다. Interceptor를 **Spring MVC** 외부의 다른 **HandlerMapping** 빈에서도 재사용하려면 `MappedInterceptor`로 선언하거나, 수동으로 등록해야 합니다.

### 자주 사용되는 Interceptor 예시

1. **LocaleChangeInterceptor**: 요청 파라미터를 통해 애플리케이션의 **locale**을 동적으로 변경할 수 있습니다. 예: `?locale=en_US`.
   
2. **ThemeChangeInterceptor**: 요청 파라미터를 기반으로 **테마**를 변경할 수 있습니다. 예: `?theme=dark`.

3. **HandlerInterceptorAdapter**: 기본적인 인터페이스를 확장하여 **preHandle, postHandle, afterCompletion** 메서드를 오버라이드해 커스텀 Interceptor를 구현할 수 있습니다.

4. **WebContentInterceptor**: HTTP 응답의 **캐싱**과 **헤더**를 제어할 수 있습니다. 주로 웹 콘텐츠의 브라우저 캐싱을 관리할 때 사용됩니다.

```java
WebContentInterceptor interceptor = new WebContentInterceptor();
interceptor.setCacheSeconds(0);  // 캐싱 비활성화
interceptor.setRequireSession(true);  // 특정 페이지에 대해 세션 요구
```

5. **커스텀 Interceptor**:
   - **MetricsInterceptor**: 요청의 성능 지표를 수집하고, 처리 시간을 기록하는 등 **성능 모니터링**을 수행할 수 있습니다.
   - **LoggingInterceptor**: 요청과 응답을 **로깅**하는 Interceptor로, 디버깅이나 감사 목적으로 사용됩니다.

### Servlet Filter와 Spring Security

Interceptor는 다양한 **공통 관심사**를 처리하는 데 적합하지만, **보안**과 관련된 작업은 더 **안정적**이고 **초기 처리 단계**에서 동작하는 **Servlet Filter**나 **Spring Security**를 사용하는 것이 좋습니다. Interceptor는 HandlerMapping 이후에 실행되기 때문에 요청 처리 초기 단계에서 수행되어야 하는 보안 작업에 적합하지 않습니다.

#### Servlet Filter와 Spring MVC Interceptor의 차이점:

| 특징                | **Servlet Filter**                  | **Spring MVC Interceptor**          |
|---------------------|-------------------------------------|-------------------------------------|
| **요청 처리 시점**   | 모든 요청에 대해 초기 적용          | 요청이 핸들러에 매핑된 후 적용      |
| **적용 범위**        | Spring MVC 외부에서도 동작 (정적 리소스) | Spring MVC 내에서만 동작            |
| **주요 사용 목적**   | 보안, 요청 수정                     | 로깅, 모델 수정, 커스텀 처리         |
| **보안 적합성**      | 보안에 적합 (인증, 인가)            | 보안에는 적합하지 않음              |

### 결론

Interceptor는 Spring MVC에서 매우 유용한 도구로, 요청과 응답에 대해 **커스텀 로직**을 적용할 수 있습니다. 주로 **로깅**, **locale 변경**, **테마 변경**, **성능 모니터링** 등에 사용되며, 요청 처리의 여러 단계에서 유연하게 사용할 수 있습니다. 그러나, **보안 작업**에는 적합하지 않으며, **Spring Security**나 **Servlet Filter**와 같은 더 강력한 보안 프레임워크를 사용하는 것이 좋습니다.

만약 HTTP 요청에 대한 **로깅**이나 **성능 지표 수집**과 같은 공통 관심사를 처리하려면 Interceptor가 적합한 선택이 될 수 있습니다. 반면에 **보안**과 관련된 작업이 필요하다면, **Spring Security**와 같은 더 강력한 프레임워크를 사용하는 것이 바람직합니다.