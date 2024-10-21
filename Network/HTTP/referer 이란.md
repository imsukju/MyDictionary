`Referer` 헤더는 HTTP 요청 시, 사용자가 현재 요청하는 페이지로 이동하기 전에 방문했던 웹 페이지의 URL을 나타내는 헤더입니다. 이를 통해 서버는 사용자가 이전에 어떤 페이지에서 현재 페이지로 넘어왔는지를 알 수 있습니다.

### Referer 헤더의 사용 예

- 사용자가 `https://example.com/page1`에서 링크를 클릭하여 `https://example.com/page2`로 이동했다면, `page2`에 대한 HTTP 요청 헤더에는 다음과 같은 `Referer` 값이 포함됩니다.
    
    ```
    Referer: <https://example.com/page1>
    
    ```
    

### 주요 기능

1. **트래픽 분석**: 사용자가 어느 경로를 통해 사이트에 방문했는지 파악할 수 있습니다.
2. **CSRF 보호**: CSRF(Cross-Site Request Forgery) 공격 방지를 위해 `Referer` 헤더를 검사하여, 요청이 신뢰할 수 있는 출처에서 오는지 확인할 수 있습니다.
3. **링크 추적**: 광고나 링크 추적 등을 통해 마케팅 효율을 분석하는 데 사용됩니다.

### 주의사항

- `Referer` 헤더에는 개인 정보나 보안 관련 정보가 포함될 수 있기 때문에, HTTPS에서 HTTP로 이동할 때 `Referer`가 자동으로 전송되지 않거나, 브라우저가 보안을 위해 `Referer` 값을 빈 값으로 설정할 수 있습니다.
- 최신 브라우저에서는 `Referer`의 민감한 정보 노출을 줄이기 위해 `Referrer-Policy` 헤더를 통해 `Referer`의 전달 방식을 제어할 수 있습니다.

