### Spring MVC Method Return Types 번역

Spring MVC의 컨트롤러 메서드에서 사용되는 반환 값 유형에 대해 설명합니다. 리액티브 유형은 모든 반환 값에 대해 지원됩니다.

| **컨트롤러 메서드 반환 값** | **설명** |
|--------------------------|--------------------------------------------------------|
| `@ResponseBody` | 반환 값을 `HttpMessageConverter` 구현체를 통해 변환하여 응답에 작성함. 자세한 내용은 [`@ResponseBody`](xref:web/webmvc/mvc-controller/ann-methods/responsebody.adoc)를 참조. |
| `HttpEntity<B>`, `ResponseEntity<B>` | 전체 응답(HTTP 헤더 및 본문 포함)을 지정하며, `HttpMessageConverter`를 통해 변환하여 응답에 작성함. 자세한 내용은 [ResponseEntity](xref:web/webmvc/mvc-controller/ann-methods/responseentity.adoc)를 참조. |
| `HttpHeaders` | 헤더만 포함된 응답을 반환함(본문 없음). |
| `ErrorResponse` | RFC 9457 오류 응답을 본문에 세부 내용을 포함하여 렌더링함. 자세한 내용은 [Error Responses](xref:web/webmvc/mvc-ann-rest-exceptions.adoc)를 참조. |
| `ProblemDetail` | RFC 9457 오류 응답을 본문에 세부 내용을 포함하여 렌더링함. 자세한 내용은 [Error Responses](xref:web/webmvc/mvc-ann-rest-exceptions.adoc)를 참조. |
| `String` | `ViewResolver` 구현체를 통해 뷰 이름을 해석하고, 암묵적 모델(커맨드 객체 및 `@ModelAttribute` 메서드를 통해 결정된 모델)을 사용하여 뷰를 렌더링함. 필요 시 `Model` 인자를 선언하여 프로그래밍 방식으로 모델을 확장할 수 있음. |
| `View` | 암묵적 모델(커맨드 객체 및 `@ModelAttribute` 메서드를 통해 결정된 모델)과 함께 사용할 `View` 인스턴스를 반환하여 뷰를 렌더링함. 필요 시 `Model` 인자를 선언하여 프로그래밍 방식으로 모델을 확장할 수 있음. |
| `java.util.Map`, `org.springframework.ui.Model` | 암묵적 모델에 추가될 속성들을 포함함. 뷰 이름은 `RequestToViewNameTranslator`를 통해 암묵적으로 결정됨. |
| `@ModelAttribute` | 모델에 추가될 속성을 포함함. 뷰 이름은 `RequestToViewNameTranslator`를 통해 암묵적으로 결정됨. `@ModelAttribute` 애노테이션은 선택적으로 사용할 수 있음. |
| `ModelAndView` 객체 | 사용할 뷰와 모델 속성을 지정하고, 선택적으로 응답 상태를 설정할 수 있음. |
| `void` | `void` 반환 타입이거나 `null` 반환 값인 메서드는 다음 조건이 충족될 경우 응답이 완전히 처리된 것으로 간주됨: `ServletResponse`, `OutputStream` 인자 존재, `@ResponseStatus` 애노테이션 사용, 컨트롤러에서 ETag 또는 `lastModified` 타임스탬프 검사를 통과한 경우. 이 조건에 해당하지 않으면, `void` 반환 타입은 REST 컨트롤러의 경우 "응답 본문 없음"을 나타내거나 HTML 컨트롤러의 경우 기본 뷰 이름 선택을 나타냄. |
| `DeferredResult<V>` | 이벤트나 콜백의 결과로 비동기적으로 앞서 언급된 반환 값 중 하나를 생성함. 자세한 내용은 [Asynchronous Requests](xref:web/webmvc/mvc-ann-async.adoc) 및 [`DeferredResult`](xref:web/webmvc/mvc-ann-async.adoc#mvc-ann-async-deferredresult)를 참조. |
| `Callable<V>` | Spring MVC에서 관리하는 스레드에서 비동기적으로 앞서 언급된 반환 값 중 하나를 생성함. 자세한 내용은 [Asynchronous Requests](xref:web/webmvc/mvc-ann-async.adoc) 및 [`Callable`](xref:web/webmvc/mvc-ann-async.adoc#mvc-ann-async-callable)를 참조. |
| `ListenableFuture<V>`, `java.util.concurrent.CompletionStage<V>`, `java.util.concurrent.CompletableFuture<V>` | `DeferredResult`의 대안으로, 예를 들어 하위 서비스가 이러한 반환 값을 제공하는 경우에 사용 가능. |
| `ResponseBodyEmitter`, `SseEmitter` | `HttpMessageConverter` 구현체를 사용하여 응답에 비동기적으로 스트림 객체를 작성함. `ResponseEntity`의 본문으로도 지원됨. 자세한 내용은 [Asynchronous Requests](xref:web/webmvc/mvc-ann-async.adoc) 및 [HTTP Streaming](xref:web/webmvc/mvc-ann-async.adoc#mvc-ann-async-http-streaming)을 참조. |
| `StreamingResponseBody` | 응답 `OutputStream`에 비동기적으로 데이터를 작성함. `ResponseEntity`의 본문으로도 지원됨. 자세한 내용은 [Asynchronous Requests](xref:web/webmvc/mvc-ann-async.adoc) 및 [HTTP Streaming](xref:web/webmvc/mvc-ann-async.adoc#mvc-ann-async-http-streaming)을 참조. |
| 리액터 및 `ReactiveAdapterRegistry`를 통해 등록된 다른 리액티브 타입 | 단일 값 유형(예: `Mono`)은 `DeferredResult` 반환과 유사하며, 다중 값 유형(예: `Flux`)은 요청된 미디어 유형에 따라 스트림으로 처리되거나, `List`로 수집되어 단일 값으로 렌더링됨. 자세한 내용은 [Asynchronous Requests](xref:web/webmvc/mvc-ann-async.adoc) 및 [Reactive Types](xref:web/webmvc/mvc-ann-async.adoc#mvc-ann-async-reactive-types)를 참조. |
| **기타 반환 값** | 다른 방법으로도 해결되지 않는 반환 값은 모델 속성으로 처리되며, 단순 타입(`BeanUtils#isSimpleProperty`)일 경우 처리되지 않고 무시됨. |

### 추가 설명
- `@ResponseBody` 또는 `ResponseEntity`를 사용하여 메서드의 반환 값을 HTTP 응답 본문으로 쉽게 작성할 수 있습니다.
- `DeferredResult`, `Callable`, `CompletableFuture` 등의 비동기 반환 유형은 비동기 요청 처리를 지원하며, 비동기적 작업의 결과를 관리하는 데 유용합니다.
- `String`, `ModelAndView`, `View`와 같은 반환 유형은 뷰를 명시적으로 지정할 때 사용되며, MVC 패턴 기반의 웹 애플리케이션에서 많이 사용됩니다.

이 표는 Spring MVC에서 다양한 요청 응답 패턴을 처리할 때 사용할 수 있는 반환 값 옵션을 제공합니다.