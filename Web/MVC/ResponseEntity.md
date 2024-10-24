`ResponseEntity`는 Spring Framework에서 HTTP 응답을 나타내는 클래스입니다. 주로 컨트롤러에서 HTTP 응답의 상태 코드, 헤더, 바디 등을 설정하고 반환할 때 사용됩니다. `ResponseEntity`를 사용하면 더 구체적으로 HTTP 응답을 제어할 수 있습니다.

### 주요 특징
- **HTTP 상태 코드 설정**: `ResponseEntity`를 사용하면 응답에 포함될 HTTP 상태 코드를 명시적으로 설정할 수 있습니다. 예를 들어, `200 OK`, `404 Not Found`, `500 Internal Server Error` 등의 상태 코드를 지정할 수 있습니다.
  
- **응답 본문 설정**: 응답 본문을 설정하여 클라이언트에게 데이터를 전달할 수 있습니다.

- **응답 헤더 설정**: 추가적인 HTTP 헤더를 응답에 포함할 수 있습니다.

### 기본 사용 예시
```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ExampleController {

    @GetMapping("/hello")
    public ResponseEntity<String> hello() {
        return new ResponseEntity<>("Hello, World!", HttpStatus.OK);
    }
}
```

이 예시에서 `/hello` 경로로 요청이 들어오면 `ResponseEntity`를 통해 응답 본문("Hello, World!")과 상태 코드 `200 OK`를 클라이언트에게 보냅니다.

### `ResponseEntity`의 장점
- 상태 코드, 헤더, 본문을 명확하게 설정할 수 있어서 복잡한 HTTP 응답을 처리할 때 유용합니다.
- API 응답에 대한 세밀한 제어가 필요할 때 효과적입니다.
