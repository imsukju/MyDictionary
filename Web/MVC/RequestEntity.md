

`RequestEntity`는 요청의 본문, 헤더, HTTP 메서드(GET, POST 등), URI 등을 명시적으로 설정할 수 있게 해줍니다. 이를 사용하면 요청을 더 정밀하게 제어할 수 있습니다.

### `RequestEntity`의 주요 특징
- **HTTP 메서드 설정**: `RequestEntity`를 사용하면 GET, POST, PUT 등과 같은 HTTP 메서드를 명확하게 설정할 수 있습니다.
- **요청 헤더 설정**: 요청에 포함될 헤더를 설정할 수 있습니다.
- **요청 본문 설정**: POST나 PUT 요청 등에서 본문 데이터를 설정할 수 있습니다.
- **URI 설정**: 요청이 전송될 목적지 URI를 설정할 수 있습니다.

### `RequestEntity` 사용 예시
다음은 Spring의 `RestTemplate`을 사용해 `RequestEntity`로 HTTP 요청을 보내는 예시입니다.

```java
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpMethod;
import org.springframework.http.RequestEntity;
import org.springframework.web.client.RestTemplate;

import java.net.URI;

public class ExampleClient {

    public void sendRequest() {
        RestTemplate restTemplate = new RestTemplate();
        HttpHeaders headers = new HttpHeaders();
        headers.set("Authorization", "Bearer token");

        RequestEntity<String> requestEntity = RequestEntity
                .method(HttpMethod.GET, URI.create("https://api.example.com/resource"))
                .headers(headers)
                .build();

        String response = restTemplate.exchange(requestEntity, String.class).getBody();
        System.out.println(response);
    }
}
```

### 주요 사용처
- **API 호출**: `RestTemplate`이나 `WebClient` 등을 사용할 때, 요청에 대한 세부 제어가 필요할 경우 유용합니다.
- **헤더 및 메서드 제어**: 요청 헤더, URI, HTTP 메서드를 명확하게 지정할 수 있어 REST API 호출을 할 때 유용합니다.

### 요약
- `RequestEntity`는 HTTP 요청을 만들 때 요청 메서드, 헤더, 본문 등을 포함한 세부적인 제어가 가능하게 해주는 클래스입니다.
- `ResponseEntity`가 HTTP 응답을 다루는 반면, `RequestEntity`는 HTTP 요청을 다룰 때 사용됩니다.
- 주로 클라이언트 측에서 API 요청을 만들 때 사용합니다.
