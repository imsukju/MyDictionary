Spring MVC의 어노테이션별 속성은 각 어노테이션이 제공하는 다양한 기능을 세부적으로 조정할 수 있도록 하는 옵션들이 있습니다. 아래에서 주요 어노테이션별로 사용할 수 있는 속성들을 추가하여 설명하겠습니다.

### 어노테이션별 속성 포함된 표

| **어노테이션**       | **설명**                                                                                         | **주요 속성**                                                                                                                   |
|----------------------|--------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| `@Controller`         | 클래스를 Spring MVC 컨트롤러로 표시. 웹 요청을 처리하는 데 사용됨.                                  | N/A                                                                                                                         |
| `@RestController`     | `@Controller`와 `@ResponseBody`를 결합하여 메서드에서 JSON/XML을 직접 반환.                          | N/A                                                                                                                         |
| `@RequestMapping`     | HTTP 요청을 컨트롤러 메서드에 매핑. 클래스 또는 메서드 수준에서 사용 가능.                              | `value`, `path`, `method`, `params`, `headers`, `consumes`, `produces`                                                      |
| `@GetMapping`         | 지정된 URL에 대해 HTTP GET 요청을 처리.                                                           | `value`, `path`, `params`, `headers`, `produces`                                                                            |
| `@PostMapping`        | 지정된 URL에 대해 HTTP POST 요청을 처리.                                                          | `value`, `path`, `params`, `headers`, `consumes`, `produces`                                                                |
| `@PutMapping`         | 지정된 URL의 리소스를 업데이트하는 HTTP PUT 요청을 처리.                                           | `value`, `path`, `params`, `headers`, `consumes`, `produces`                                                                |
| `@DeleteMapping`      | 지정된 URL의 리소스를 삭제하는 HTTP DELETE 요청을 처리.                                            | `value`, `path`, `params`, `headers`, `produces`                                                                            |
| `@PatchMapping`       | 지정된 URL에 대해 리소스의 일부 업데이트를 위한 HTTP PATCH 요청을 처리.                             | `value`, `path`, `params`, `headers`, `consumes`, `produces`                                                                |
| `@RequestParam`       | HTTP 요청 파라미터를 메서드 파라미터에 바인딩.                                                    | `value`, `required`, `defaultValue`                                                                                         |
| `@PathVariable`       | URI 템플릿 변수를 메서드 파라미터에 바인딩.                                                       | `value`, `required`                                                                                                         |
| `@ModelAttribute`     | 모델 객체를 폼 데이터에 바인딩하여 컨트롤러에서 사용.                                              | `value`, `binding`                                                                                                          |
| `@RequestBody`        | HTTP 요청 본문을 메서드 파라미터로 매핑 (POST/PUT 요청에서 사용).                                   | `required`                                                                                                                  |
| `@ResponseBody`       | 메서드의 반환값을 응답 본문으로 사용함을 나타냄 (REST API에서 사용).                                  | N/A                                                                                                                         |
| `@RequestHeader`      | 특정 HTTP 헤더를 메서드 파라미터에 바인딩.                                                         | `value`, `required`, `defaultValue`                                                                                         |
| `@RequestPart`        | 멀티파트 파일 요청 부분을 메서드 파라미터에 바인딩 (파일 업로드에 사용).                             | `value`, `required`                                                                                                         |
| `@SessionAttributes`  | 세션에서 속성을 저장하고 여러 요청에 걸쳐 관리함.                                                   | `value`, `types`                                                                                                            |
| `@SessionAttribute`   | 세션에서 특정 세션 속성을 가져옴.                                                                 | `value`, `required`                                                                                                         |
| `@ExceptionHandler`   | 핸들러 메서드에서 발생한 예외를 처리하는 메서드를 정의.                                            | `value` (예외 클래스)                                                                                                       |
| `@InitBinder`         | 요청 파라미터를 Java 객체에 바인딩하는 방식을 커스터마이즈.                                           | `value` (바인딩할 객체 이름)                                                                                                |
| `@CrossOrigin`        | 컨트롤러 메서드에 CORS(교차 출처 리소스 공유)를 활성화.                                             | `origins`, `allowedHeaders`, `exposedHeaders`, `methods`, `maxAge`, `allowCredentials`                                      |

### 주요 속성 설명
- **`value`**: 매핑할 경로나 변수명을 지정합니다.
- **`path`**: `value`와 동일하게 경로를 지정하는 속성입니다.
- **`method`**: 허용할 HTTP 메서드를 지정합니다. (예: `RequestMethod.GET`)
- **`params`**: 요청에 필요한 파라미터 조건을 지정합니다.
- **`headers`**: 특정 HTTP 헤더 조건을 지정합니다.
- **`consumes`**: 요청에서 수용할 미디어 타입 (예: JSON, XML)을 지정합니다.
- **`produces`**: 응답에서 생성할 미디어 타입을 지정합니다.
- **`required`**: 값이 필수인지 여부를 지정합니다. (기본값: `true`)
- **`defaultValue`**: 파라미터가 없을 때 사용할 기본값을 지정합니다.
- **`binding`**: 객체의 바인딩 여부를 지정합니다.
- **`origins`**: 허용할 출처(origin)를 지정합니다.

이 표를 통해 Spring MVC 어노테이션과 그 속성들을 함께 이해할 수 있습니다.