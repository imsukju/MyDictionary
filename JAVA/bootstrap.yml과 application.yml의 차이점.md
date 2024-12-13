### **`bootstrap.yml`과 `application.yml`의 차이점**

Spring Boot 애플리케이션은 설정 파일로 `bootstrap.yml`과 `application.yml`을 사용할 수 있습니다.

이 두 파일은 역할과 동작 시점에서 차이가 있습니다.

---

### **1. `bootstrap.yml`**

### **역할**

- Spring Boot 애플리케이션이 **시작되기 전에 로드되는 설정 파일**.
- 애플리케이션의 초기화 과정에서 필요한 설정을 담습니다.
- 주로 **Spring Cloud Config**와 같은 외부 설정 서버와 통신하기 위한 설정에 사용됩니다.

### **특징**

1. **부트스트랩 컨텍스트**:
    - 애플리케이션 컨텍스트보다 먼저 초기화됩니다.
    - Config Server, 암호화 설정, 환경별 프로파일 등을 초기화하는 데 사용됩니다.
2. **우선순위**:
    - `bootstrap.yml`의 설정은 `application.yml`보다 **우선 적용**됩니다.
    - Config Server에서 반환된 설정이 `application.yml`의 값을 덮어씁니다.
3. **사용 사례**:
    - Config Server 연결 정보 (`spring.cloud.config.uri`).
    - 암호화/복호화 설정.
    - 클라우드 서비스와 관련된 초기 설정.

---

### **`bootstrap.yml` 예제**

```yaml
spring:
  application:
    name: organization-service  # Config Server에서 찾을 서비스 이름
  cloud:
    config:
      uri: http://configserver:8071  # Config Server 주소
      fail-fast: true               # Config Server 연결 실패 시 애플리케이션 실행 중단
  profiles:
    active: dev                    # 활성화할 프로파일

```

**설명**:

- `spring.application.name`: Config Server에서 설정 파일을 찾을 때 사용하는 이름.
- `spring.cloud.config.uri`: Config Server와 연결하기 위한 URI.
- `spring.profiles.active`: 기본 활성화 프로파일 설정.

---

### **동작 흐름**

1. Spring Boot가 실행되면서 `bootstrap.yml`을 가장 먼저 로드.
2. Config Server와 연결하여 설정 파일을 가져옴.
3. Config Server에서 가져온 설정은 Spring Boot의 `Environment`에 반영.
4. 이후, `application.yml`을 로드하여 나머지 설정을 추가.

---

### **2. `application.yml`**

### **역할**

- Spring Boot 애플리케이션의 **주요 설정 파일**.
- 애플리케이션 실행에 필요한 대부분의 설정을 담습니다.

### **특징**

1. **애플리케이션 컨텍스트**:
    - `application.yml`은 `bootstrap.yml` 이후에 로드됩니다.
    - 애플리케이션의 동작과 관련된 설정을 담습니다.
2. **우선순위**:
    - `bootstrap.yml`에서 설정된 값이 `application.yml`의 값을 덮어씁니다.
    - 단, `application.yml`에서 명시적으로 설정된 값은 기본적으로 더 낮은 우선순위를 가집니다.
3. **사용 사례**:
    - 데이터베이스 연결 정보.
    - 애플리케이션 서버 포트.
    - 로깅 레벨 설정.
    - 일반적인 비즈니스 로직 관련 설정.

---

### **`application.yml` 예제**

```yaml
server:
  port: 8081                      # 애플리케이션이 사용할 포트
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/dev_db
    username: dev_user
    password: dev_password
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
logging:
  level:
    com.example: DEBUG             # 특정 패키지의 로깅 레벨 설정

```

**설명**:

- `server.port`: 애플리케이션의 실행 포트.
- `spring.datasource`: 데이터베이스 연결 설정.
- `logging.level`: 애플리케이션 로그 레벨.

---

### **3. 두 파일의 주요 차이점**

| **특징** | **`bootstrap.yml`** | **`application.yml`** |
| --- | --- | --- |
| **로드 시점** | Spring Boot 시작 시, 가장 먼저 로드 | `bootstrap.yml` 이후 로드 |
| **적용 범위** | Config Server, 초기화 단계 설정 | 애플리케이션 동작에 필요한 설정 |
| **우선순위** | 더 높은 우선순위를 가짐 | `bootstrap.yml`의 설정에 덮어질 수 있음 |
| **사용 사례** | Config Server URI, 암호화 설정, 초기 프로파일 | 데이터베이스 설정, 서버 포트, 로깅 설정 |
| **의존성** | 주로 Spring Cloud Config와 함께 사용 | 기본적인 Spring Boot 설정 파일 |

---

### **4. 예제: Config Server와 연동된 `bootstrap.yml` + `application.yml`**

### **`bootstrap.yml`**

```yaml
spring:
  application:
    name: organization-service       # 서비스 이름
  cloud:
    config:
      uri: http://configserver:8071  # Config Server URI
      fail-fast: true                # Config Server 연결 실패 시 중단
  profiles:
    active: dev                      # 기본 프로파일

```

### **`application.yml`**

```yaml
server:
  port: 8081                       # 애플리케이션 실행 포트
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/dev_db
    username: dev_user
    password: dev_password
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
logging:
  level:
    com.example: DEBUG

```

---

### **5. 실제 동작 예시**

1. 애플리케이션 시작:
    - `bootstrap.yml`이 먼저 로드.
    - Config Server와 연결(`http://configserver:8071`)하여 설정 파일(`organization-service-dev.properties`)을 가져옴.
2. 가져온 설정:
    
    ```
    spring.datasource.url=jdbc:mysql://config-db:3306/remote_dev_db
    spring.datasource.username=config_user
    spring.datasource.password=config_password
    
    ```
    
3. `application.yml` 로드:
    - `spring.datasource.url`, `spring.datasource.username`, `spring.datasource.password`는 이미 Config Server에서 받은 설정으로 덮어씀.
    - `server.port`와 로깅 설정은 그대로 유지.
4. 최종 적용 설정:
    - 데이터베이스 설정: Config Server에서 받은 값.
    - 서버 포트: `application.yml` 값(8081).

---

### **6. 요약**

- **`bootstrap.yml`**:
    - Config Server와 같은 외부 시스템과의 초기 통신 설정.
    - Spring Boot 시작 전에 로드.
    - 우선순위가 높음.
- **`application.yml`**:
    - 애플리케이션 실행과 관련된 주요 설정.
    - `bootstrap.yml` 이후에 로드.
    - 일반적인 설정을 담음.

**결론**:

`bootstrap.yml`은 초기화용, `application.yml`은 실행용 설정 파일입니다. 두 파일은 서로 보완적인 역할을 하며, Config Server와 통합 시 `bootstrap.yml`이 필수적으로 사용됩니다.