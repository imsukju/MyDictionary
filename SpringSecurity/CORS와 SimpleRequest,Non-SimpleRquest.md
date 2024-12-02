
### 1. CORS의 기본 원리

웹 브라우저는 기본적으로 **동일 출처 정책(Same-Origin Policy)**을 따릅니다. 이 정책은 보안 강화와 사용자 정보 보호를 위해 고안된 것으로, 웹 애플리케이션이 외부 리소스에 무분별하게 접근하는 것을 막기 위해 있습니다.

- **Origin(출처)**: URL의 프로토콜, 호스트, 포트로 구성된 출처 개념입니다. 웹 브라우저는 자바스크립트에서 동일한 Origin(예: `https://example.com`)을 따르는 자원에 대해서만 접근을 허용합니다.
  
  예를 들어, `https://example.com`에서 스크립트가 실행 중이라면 `https://api.example.com`이나 `http://example.com:3000`은 모두 **다른 Origin**으로 간주됩니다.

- **CORS 도입의 이유**: 웹 애플리케이션이 다양한 API에 접근할 필요가 증가하면서, 동일 출처 정책을 완화하고 안전한 요청을 허용하기 위해 CORS가 도입되었습니다. 서버는 CORS 설정을 통해 어떤 출처에 접근을 허용할지 선택할 수 있습니다.

### 2. CORS 요청의 유형: Simple Request와 Non-Simple Request

CORS 요청은 두 가지 유형으로 나뉩니다:
1. **Simple Request**: 단순한 요청 형식으로, 브라우저가 사전 확인 없이 서버에 바로 전송할 수 있는 요청입니다.
2. **Non-Simple Request(Preflight Request)**: 브라우저가 요청을 보내기 전에 사전 확인(Preflight) 과정을 거쳐 서버가 요청을 허용하는지 확인해야 하는 요청입니다.

---

### 3. Simple Request

Simple Request는 브라우저가 **사전 확인 없이 바로 서버에 요청을 보낼 수 있는 요청 형식**입니다. 이를 위해서는 특정 조건을 충족해야 합니다.

#### Simple Request의 조건
1. **HTTP 메서드**가 GET, POST, 또는 HEAD 중 하나여야 합니다. 이는 데이터 조회, 간단한 전송 용도의 안전한 메서드입니다.
  
2. **허용된 헤더만 사용**:
   - Simple Request는 기본적인 헤더만 포함해야 하며, 다른 헤더나 인증 정보는 사용할 수 없습니다.
   - 사용 가능한 기본 헤더:
     - `Accept`
     - `Accept-Language`
     - `Content-Language`
     - `Content-Type` (단, Content-Type은 `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain` 중 하나로 제한됨)

3. **Content-Type 조건**:
   - POST 메서드로 데이터를 전송할 때 Content-Type이 반드시 `application/x-www-form-urlencoded`, `multipart/form-data`, 또는 `text/plain`이어야 합니다.
   - 이 외의 `application/json`, `text/html` 등의 타입은 Simple Request 조건에서 제외됩니다.

#### Simple Request의 동작 과정
- 브라우저는 Simple Request 조건을 충족할 때 서버에 바로 요청을 보냅니다.
- 서버는 요청을 받고, CORS 허용 설정에 따라 응답을 반환합니다.
  - 서버가 응답에 `Access-Control-Allow-Origin` 헤더를 포함하면 브라우저는 요청을 정상적으로 받아들이고 데이터를 처리합니다.
  - 해당 헤더가 없거나 요청을 허용하지 않는 경우, 브라우저는 응답을 차단하고 오류 메시지를 표시합니다.

**Simple Request 예제**
```javascript
// Simple Request를 위한 자바스크립트 코드
fetch('https://api.example.com/data', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded' // Simple Request 조건 충족
  },
  body: 'name=John&age=30'
}).then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

---

### 4. Non-Simple Request (Preflight Request)

Simple Request 조건을 충족하지 못하는 요청은 **Non-Simple Request**로 간주되며, 이 경우 브라우저는 **Preflight 요청**이라는 사전 확인 요청을 먼저 서버에 보내서 안전성을 확인합니다.

#### Non-Simple Request의 조건
Non-Simple Request에 해당하는 경우는 다음과 같습니다:
- **메서드가 GET, POST, HEAD 이외의 메서드**일 때 (예: PUT, DELETE).
- **커스텀 헤더**를 사용할 때 (`X-Custom-Header` 등).
- Content-Type이 Simple Request의 허용 범위를 벗어난 `application/json`, `text/html` 등일 때.

#### Preflight 요청 과정
Non-Simple Request는 브라우저가 **OPTIONS** 메서드를 사용하여 서버에 허용 여부를 묻는 사전 요청을 먼저 보내는 방식입니다. 브라우저는 서버가 허용 응답을 반환하는 경우에만 본 요청을 보냅니다.

**Preflight 요청 흐름**
1. **Preflight 요청 전송**: 브라우저가 OPTIONS 메서드로 서버에 Preflight 요청을 보냅니다. Preflight 요청에는 본 요청의 메서드와 사용될 헤더가 포함됩니다.
   
   예시:
   ```http
   OPTIONS /data HTTP/1.1
   Host: api.example.com
   Origin: https://example.com
   Access-Control-Request-Method: PUT
   Access-Control-Request-Headers: X-Custom-Header
   ```

2. **서버 응답**: 서버는 OPTIONS 요청을 받고, 허용할 메서드와 헤더 등을 응답에 포함해 브라우저에 반환합니다. 서버가 응답에서 요청을 허용하지 않으면 브라우저는 본 요청을 차단합니다.
   
   예시 응답:
   ```http
   HTTP/1.1 204 No Content
   Access-Control-Allow-Origin: https://example.com
   Access-Control-Allow-Methods: GET, POST, PUT
   Access-Control-Allow-Headers: X-Custom-Header
   ```

3. **본 요청 전송**: 브라우저는 Preflight가 허용된 경우에만 본 요청을 전송합니다. 본 요청은 일반적으로 `PUT`, `DELETE` 등의 메서드를 사용하거나 커스텀 헤더를 포함할 수 있습니다.

4. **본 요청 응답 처리**: 서버는 본 요청을 처리한 후, 응답 헤더에 `Access-Control-Allow-Origin`을 포함해 브라우저에 반환합니다. 브라우저는 허용된 Origin일 때만 데이터를 처리하고 출력합니다.

**Non-Simple Request 예제**
```javascript
// Non-Simple Request 예제
fetch('https://api.example.com/data', {
  method: 'PUT',
  headers: {
    'Content-Type': 'application/json',
    'X-Custom-Header': 'custom-value'
  },
  body: JSON.stringify({ name: 'John', age: 30 })
}).then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

---

### 5. CORS 관련 주요 헤더 설명

CORS 요청과 응답에는 특정 HTTP 헤더가 필요합니다. 각 헤더는 브라우저가 요청을 허용할지 판단하는 중요한 정보를 제공합니다.

- **Access-Control-Allow-Origin**: 허용할 출처를 명시하는 헤더입니다. 특정 도메인을 명시하거나 `*`을 통해 모든 도메인을 허용할 수 있습니다. 단, `*`은 자격 증명(Credentials)을 포함한 요청에는 사용할 수 없습니다.
  
- **Access-Control-Allow-Methods**: 서버가 허용할 HTTP 메서드들을 명시합니다. Preflight 요청에 대한 응답에서 필요한 헤더로, GET, POST, PUT, DELETE 등을 포함할 수 있습니다.

- **Access-Control-Allow-Headers**: 요청에서 사용할 수 있는 커스텀 헤더를 정의합니다. 예를 들어, `X-Custom-Header`가 있다면 이 헤더를 명시해야 본 요청이 가능합니다.

- **Access-Control-Allow-Credentials**: 요청에 자격 증명(쿠키, 인증 정보 등)을 포함할 수 있는지 여부를 결정합니다. `true`로 설정하면 허용합니다.

- **Access-Control-Max-Age**: Preflight 요청의 유효 기간을 초 단위로 지정합니다. 이 시간 동안 동일한 조건의 요청에 대해 Preflight 요청 없이 본 요청만 보낼 수 있습니다.

---

### 6. 요약

- **CORS**는 웹 보안을 위해 출처 간 요청을 제한하고 허용 여부를 서버에서 설정합니다.
- **Simple Request**는 사전 확인 없이 바로 요청이 전송되는 경우이며, 특정 조건을 충족해야 합니다.
- **Non-Simple Request**는 브라우저가 사전 확인 요청을 먼저 보내고, 서버가 허용하는 경우에만 본 요청을 수행하는 방식입니다.
