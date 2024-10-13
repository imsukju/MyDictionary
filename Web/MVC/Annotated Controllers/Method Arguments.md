### Spring MVC Method Arguments
[원본](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/arguments.html)  

Spring MVC에서 사용 가능한 컨트롤러 메서드 인자에 대해 설명합니다. 리액티브 유형은 어떤 인자에서도 지원되지 않습니다.

JDK 8의 `java.util.Optional`은 `@RequestParam`, `@RequestHeader` 등의 `required` 속성을 가진 애노테이션과 함께 사용될 때 지원되며, 이 경우 `required=false`와 동일하게 처리됩니다.

| **컨트롤러 메서드 인자** | **설명** |
|------------------------|--------------------------------------------------------|
| `WebRequest`, `NativeWebRequest` | Servlet API를 직접 사용하지 않고 요청 파라미터와 요청 및 세션 속성에 접근할 수 있음. |
| `jakarta.servlet.ServletRequest`, `jakarta.servlet.ServletResponse` | 예: `ServletRequest`, `HttpServletRequest`, `MultipartRequest`, `MultipartHttpServletRequest` 등의 요청 및 응답 유형 사용 가능. |
| `jakarta.servlet.http.HttpSession` | 세션이 존재하는 것을 보장함. 이 인자는 절대 `null`이 아님. 세션 접근은 스레드 안전하지 않으므로 동시 접근을 허용할 경우 `RequestMappingHandlerAdapter`의 `synchronizeOnSession` 플래그를 `true`로 설정해야 함. |
| `jakarta.servlet.http.PushBuilder` | HTTP/2 리소스 푸시를 위한 Servlet 4.0의 푸시 빌더 API. 클라이언트가 HTTP/2를 지원하지 않을 경우, 주입된 `PushBuilder` 인스턴스가 `null`일 수 있음. |
| `java.security.Principal` | 현재 인증된 사용자 정보를 포함하며, 필요에 따라 특정 `Principal` 구현 클래스가 사용될 수 있음. Spring Security의 `Authentication` 클래스가 `Principal`을 구현하고 있으며, `HttpServletRequest#getUserPrincipal`을 통해 주입됨. `@AuthenticationPrincipal` 애노테이션과 함께 사용하면 `Authentication#getPrincipal`을 통해 Spring Security의 커스텀 리졸버가 처리함. |
| `HttpMethod` | 요청의 HTTP 메서드를 나타냄. |
| `java.util.Locale` | 현재 요청의 Locale 정보로, 설정된 `LocaleResolver` 또는 `LocaleContextResolver`에 의해 결정됨. |
| `java.util.TimeZone`, `java.time.ZoneId` | 현재 요청의 타임존 정보로, `LocaleContextResolver`에 의해 결정됨. |
| `java.io.InputStream`, `java.io.Reader` | Servlet API를 통해 원시 요청 본문에 접근할 때 사용. |
| `java.io.OutputStream`, `java.io.Writer` | Servlet API를 통해 원시 응답 본문에 접근할 때 사용. |
| `@PathVariable` | URI 템플릿 변수에 접근할 때 사용. URI 템플릿은 Spring의 `@RequestMapping`에서 사용. |
| `@MatrixVariable` | URI 경로의 세그먼트에 있는 이름-값 쌍에 접근할 때 사용. |
| `@RequestParam` | Servlet 요청 파라미터에 접근할 때 사용. 파라미터 값은 선언된 메서드 인자 타입으로 변환됨. 일반적인 요청 파라미터 외에도 multipart 파일에 접근할 때 사용 가능. |
| `@RequestHeader` | 요청 헤더에 접근할 때 사용. 헤더 값은 선언된 메서드 인자 타입으로 변환됨. |
| `@CookieValue` | 쿠키 값에 접근할 때 사용. 쿠키 값은 선언된 메서드 인자 타입으로 변환됨. |
| `@RequestBody` | HTTP 요청 본문에 접근할 때 사용. 본문 내용은 `HttpMessageConverter` 구현체를 사용하여 선언된 메서드 인자 타입으로 변환됨. |
| `HttpEntity<B>` | 요청 헤더 및 본문에 접근할 때 사용. 본문은 `HttpMessageConverter`를 통해 변환됨. |
| `@RequestPart` | `multipart/form-data` 요청의 특정 파트에 접근하며, 해당 파트의 본문은 `HttpMessageConverter`를 통해 변환됨. |
| `java.util.Map`, `org.springframework.ui.Model`, `org.springframework.ui.ModelMap` | HTML 컨트롤러에서 모델에 접근하여 뷰 렌더링 시 템플릿에 노출됨. |
| `RedirectAttributes` | 리다이렉트 시 사용할 속성을 지정하며, 임시로 저장하여 다음 요청에서 접근할 수 있는 플래시 속성도 포함함. |
| `@ModelAttribute` | 모델에 있는 기존 속성에 접근하거나, 존재하지 않으면 인스턴스화하여 데이터 바인딩 및 유효성 검사를 수행함. `@ModelAttribute` 애노테이션은 선택적으로 사용할 수 있음. |
| `Errors`, `BindingResult` | 유효성 검사 및 데이터 바인딩 결과에 대한 에러 정보를 제공함. `@ModelAttribute` 인자 또는 `@RequestBody`, `@RequestPart` 인자의 유효성 검사를 수행할 때 바로 뒤에 선언해야 함. |
| `SessionStatus` + class-level `@SessionAttributes` | 세션 속성의 정리를 위해 폼 처리가 완료되었음을 표시함. `@SessionAttributes` 애노테이션을 사용하여 클래스 레벨에서 세션 속성을 관리함. |
| `UriComponentsBuilder` | 현재 요청의 호스트, 포트, 스키마, 컨텍스트 경로, 서블릿 매핑을 기반으로 URL을 준비할 때 사용함. |
| `@SessionAttribute` | 클래스 레벨의 `@SessionAttributes` 선언과 달리, 모든 세션 속성에 접근할 때 사용함. |
| `@RequestAttribute` | 요청 속성에 접근할 때 사용함. |
| **기타 인자** | 단순 타입일 경우 `@RequestParam`으로 처리되며, 복잡한 타입일 경우 `@ModelAttribute`로 처리됨. |

### 추가 설명
- `@RequestParam`, `@RequestHeader`, `@CookieValue` 등의 애노테이션은 기본적으로 `required` 속성을 가지고 있으며, `java.util.Optional`과 함께 사용하여 필수 항목이 아닌 인자를 처리할 수 있습니다.
- `Errors` 또는 `BindingResult`는 유효성 검사 실패 시 에러 정보를 포함하므로, 반드시 검증 대상 인자 바로 뒤에 위치해야 합니다.
- `@ModelAttribute`는 데이터 바인딩 및 검증을 통해 폼 데이터를 처리할 때 유용하며, `Errors`와 함께 사용할 수 있습니다. 

이 표와 설명은 Spring MVC에서 다양한 요청과 응답을 처리할 때 적절한 메서드 인자들을 선택하고 사용할 수 있도록 도움을 줍니다.