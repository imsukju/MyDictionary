
### 1. **REST(Representational State Transfer)란?**

REST는 **웹 상에서 분산 시스템을 설계하기 위한 아키텍처 스타일**입니다. REST는 Roy Fielding이 2000년 논문에서 처음 소개했으며, 웹의 장점을 최대한 활용할 수 있도록 설계되었습니다.

### REST의 주요 개념:

- **리소스(Resource)**
    
    REST는 모든 것을 "리소스"로 간주합니다. 리소스는 URI(Uniform Resource Identifier)를 통해 고유하게 식별됩니다.
    
    예:
    
    - `https://api.example.com/users/1` → 특정 사용자를 나타내는 리소스
    - `https://api.example.com/products` → 모든 상품 목록을 나타내는 리소스
- **표현(Representation)**
    
    리소스는 JSON, XML, HTML, Plain Text 등 다양한 형식으로 표현됩니다. 클라이언트와 서버 간에는 리소스의 "표현"이 교환됩니다.
    
- **상태(State)**
    
    REST에서는 클라이언트와 서버의 상태를 명시적으로 저장하지 않고 각 요청이 독립적으로 처리됩니다. 이를 **무상태성(Statelessness)**이라고 합니다.
    

---

### 2. **REST의 6가지 제약 조건**

RESTful 아키텍처는 아래 제약 조건을 따를 때 제대로 작동합니다:

1. **클라이언트-서버(Client-Server)**
    - 클라이언트는 사용자 인터페이스를 담당하고, 서버는 데이터 저장 및 처리 로직을 담당합니다.
    - 클라이언트와 서버는 서로 독립적으로 개발, 배포될 수 있습니다.
2. **무상태성(Statelessness)**
    - 서버는 클라이언트의 이전 요청 상태를 저장하지 않습니다.
    - 클라이언트 요청에는 필요한 모든 정보(인증 토큰, 요청 데이터 등)가 포함되어야 합니다.
3. **캐시 가능(Cacheability)**
    - 응답 데이터는 캐싱될 수 있어야 합니다.
    예: HTTP 헤더를 사용해 `Cache-Control`을 설정하면 클라이언트는 데이터를 재사용할 수 있습니다.
4. **계층화 시스템(Layered System)**
    - 클라이언트는 중간 계층(예: 로드 밸런서, 프록시 서버)을 통해 서버와 통신할 수 있습니다.
    - 중간 계층은 REST의 동작에 영향을 미치지 않습니다.
5. **통합 인터페이스(Uniform Interface)**
    - 리소스 식별: URI로 고유하게 식별
    - 리소스 조작: HTTP 메서드 사용(GET, POST, PUT, DELETE 등)
    - 자기 설명적 메시지: 메시지에는 처리에 필요한 모든 정보가 포함
    - HATEOAS(Hypermedia As The Engine Of Application State): 응답에 링크를 포함하여 클라이언트가 다음 행동을 결정 가능
6. **코드 온 디맨드(Code on Demand)** (선택적)
    - 서버는 클라이언트에 실행 가능한 코드를 제공할 수 있습니다. 예를 들어, JavaScript 코드 전송.

---

### 3. **RESTful API란?**

RESTful API는 **REST 아키텍처 원칙을 따르는 API**를 의미합니다. 주로 HTTP 프로토콜을 사용하여 클라이언트와 서버 간에 데이터를 주고받습니다.

### RESTful API의 특징:

1. **HTTP 메서드 사용**
    
    RESTful API는 리소스의 조작을 위해 HTTP 메서드를 사용합니다.
    
    - **GET**: 리소스 조회
    예: `GET /users/1` → 특정 사용자 정보 조회
    - **POST**: 리소스 생성
    예: `POST /users` → 새로운 사용자 생성
    - **PUT**: 리소스 수정
    예: `PUT /users/1` → 사용자 정보 업데이트
    - **DELETE**: 리소스 삭제
    예: `DELETE /users/1` → 특정 사용자 삭제
2. **URI 설계**
    
    URI는 리소스를 직관적으로 나타내야 합니다.
    
    - 예:
        - `/users` → 사용자 목록
        - `/users/1` → 특정 사용자
3. **무상태 통신**
    
    클라이언트 요청에는 요청 처리에 필요한 모든 정보가 포함됩니다. 서버는 클라이언트의 상태를 기억하지 않습니다.
    
4. **표현(Representation)**
    
    리소스는 JSON이나 XML과 같은 형식으로 반환됩니다.
    
    JSON은 가독성과 효율성 때문에 가장 널리 사용됩니다.
    

---

### 4. **RESTful API의 예제**

**요구 사항:** 사용자 데이터를 처리하는 RESTful API를 설계합니다.

### 1) 사용자 목록 조회 (GET)

```
GET /users HTTP/1.1
Host: api.example.com

```

**응답**

```json
[
  {"id": 1, "name": "Alice"},
  {"id": 2, "name": "Bob"}
]

```

### 2) 특정 사용자 조회 (GET)

```
GET /users/1 HTTP/1.1
Host: api.example.com

```

**응답**

```json
{
  "id": 1,
  "name": "Alice",
  "email": "alice@example.com"
}

```

### 3) 새로운 사용자 생성 (POST)

```
POST /users HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "name": "Charlie",
  "email": "charlie@example.com"
}

```

**응답**

```
201 Created
Location: /users/3

```

### 4) 사용자 정보 수정 (PUT)

```
PUT /users/1 HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "name": "Alice Smith",
  "email": "alice.smith@example.com"
}

```

**응답**

```
200 OK

```

### 5) 사용자 삭제 (DELETE)

```
DELETE /users/1 HTTP/1.1
Host: api.example.com

```

**응답**

```
204 No Content

```

---

### 5. **RESTful API의 장점**

1. **확장성**: 서버와 클라이언트가 독립적으로 확장 가능.
2. **유연성**: 다양한 클라이언트(Android, iOS, Web)에서 접근 가능.
3. **가독성**: 직관적인 URI와 표준 HTTP 메서드를 사용.
4. **캐싱 지원**: HTTP의 캐싱 메커니즘 활용 가능.

---

### 6. **RESTful API의 단점**

1. **HATEOAS 구현의 어려움**: 실제 구현에서 HATEOAS가 자주 생략됨.
2. **HTTP 의존성**: HTTP 외의 프로토콜(예: WebSocket)에서는 비효율적.
3. **오버헤드**: 무상태성을 유지하기 위해 요청마다 필요한 정보를 반복 전송.

---

이처럼 REST는 리소스를 효과적으로 관리하고 API 설계를 단순화하기 위한 강력한 아키텍처 패턴입니다. RESTful API는 이 원칙을 적용하여 클라이언트와 서버 간에 효율적으로 데이터를 주고받는 방식을 정의합니다.