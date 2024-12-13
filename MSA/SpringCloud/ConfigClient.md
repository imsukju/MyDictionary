### **Config Client란?**

**Config Client**는 **Spring Cloud Config Server**와 연결하여 설정 파일을 가져오는 역할을 하는 **클라이언트 애플리케이션**입니다.

Config Client는 Config Server로부터 설정 파일을 받아 자신의 환경(`Environment`)에 반영하고, 이를 바탕으로 애플리케이션을 초기화합니다.

---

### **Config Client의 주요 역할**

1. **중앙화된 설정 관리**:
    - Config Server에서 설정 파일을 가져와 애플리케이션 초기화 시 사용.
    - 클라이언트 애플리케이션은 로컬에 설정 파일을 저장할 필요가 없음.
2. **환경별 설정 관리**:
    - 개발(`dev`), 테스트(`qa`), 운영(`prod`) 등 환경별로 다른 설정 파일을 Config Server에서 가져옵니다.
3. **실시간 설정 변경**:
    - Spring Cloud Bus와 함께 사용하면 Config Server에서 설정 변경 시 이를 실시간으로 반영 가능.

---

### **Config Client와 Config Server의 관계**

### 1. Config Server

- 모든 설정 파일을 중앙화하여 관리.
- 설정 파일은 Git, 파일 시스템, 데이터베이스 등에서 관리 가능.

### 2. Config Client

- Config Server에 연결하여 설정 파일을 요청.
- 반환받은 설정을 `Environment`에 반영.

---

### **Config Client의 동작 원리**

### **1. 설정 요청**

Config Client는 애플리케이션 시작 시 Config Server에 설정 파일을 요청합니다.

요청 URL의 형식은 다음과 같습니다:

```
http://configserver:port/{application}/{profile}/{label}

```

- `{application}`: 클라이언트 애플리케이션 이름.
- `{profile}`: 환경 프로파일 (예: `dev`, `prod`).
- `{label}`: Git 브랜치 또는 태그 (기본값: `main`).

### **2. Config Server 응답**

Config Server는 요청받은 설정 파일을 읽고, 클라이언트에 JSON 형태로 반환합니다.

### **3. 설정 반영**

Config Client는 반환받은 설정을 자신의 `Environment`에 반영하여 애플리케이션이 해당 설정을 사용하도록 합니다.

---

### **Config Client 설정**

### **1. 의존성 추가**

Config Client를 사용하려면 **`spring-cloud-starter-config`** 의존성을 추가해야 합니다.

**`pom.xml`**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>

```

---

### **2. 클라이언트 설정 파일**

Config Client는 Config Server와 연결하기 위해 `application.yml` 또는 `application.properties` 파일에 설정을 추가해야 합니다.

**`application.yml` 예시**

```yaml
spring:
  application:
    name: organization-service  # 클라이언트 애플리케이션 이름
  profiles:
    active: dev                 # 활성화된 환경 프로파일
  cloud:
    config:
      uri: http://configserver:8071  # Config Server의 주소

```

---

### **3. Config Client 동작 예시**

1. Config Client가 Config Server에 요청:
    
    ```
    GET http://configserver:8071/organization-service/dev
    
    ```
    
    - `organization-service`: 클라이언트 애플리케이션 이름.
    - `dev`: 활성화된 프로파일.
2. Config Server가 응답:
    
    ```json
    {
      "name": "organization-service",
      "profiles": ["dev"],
      "propertySources": [
        {
          "name": "organization-service-dev.properties",
          "source": {
            "spring.datasource.url": "jdbc:mysql://dev-database:3306/dev_db",
            "spring.datasource.username": "dev_user",
            "spring.datasource.password": "dev_password"
          }
        }
      ]
    }
    
    ```
    
3. Config Client는 반환받은 설정(`spring.datasource.url`, `spring.datasource.username` 등)을 자신의 `Environment`에 추가.

---

### **Config Client에서 설정 사용**

Config Server에서 가져온 설정은 **`@Value`** 또는 **`@ConfigurationProperties`**를 사용하여 접근할 수 있습니다.

### **1. `@Value` 사용**

```java
@RestController
public class ConfigController {

    @Value("${spring.datasource.url}")
    private String datasourceUrl;

    @GetMapping("/config")
    public String getConfig() {
        return "Datasource URL: " + datasourceUrl;
    }
}

```

### **2. `@ConfigurationProperties` 사용**

```java
@ConfigurationProperties(prefix = "spring.datasource")
@Data
public class DataSourceConfig {
    private String url;
    private String username;
    private String password;
}

```

---

### **Config Client에서 실시간 설정 변경**

Spring Cloud Bus를 사용하면 Config Server의 설정 변경 사항을 실시간으로 반영할 수 있습니다.

### **1. Spring Cloud Bus 의존성 추가**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>

```

### **2. `/actuator/refresh`로 변경 사항 반영**

Spring Cloud Bus를 설정한 후, Config Server에서 설정 파일을 변경하면 `POST /actuator/refresh` 엔드포인트를 호출하여 변경 사항을 클라이언트에 반영할 수 있습니다.

---

### **Config Client 장점**

1. **설정 중앙화**:
    - 모든 설정 파일을 Config Server에서 관리하고, 클라이언트는 이를 가져오기만 하면 됩니다.
2. **환경별 설정 지원**:
    - 개발(`dev`), 테스트(`qa`), 운영(`prod`) 환경에 따라 다른 설정을 가져올 수 있습니다.
3. **동적 변경 지원**:
    - 설정 파일을 수정해도 서비스를 재시작하지 않고 변경 사항을 반영할 수 있습니다.

---

### **실제 시나리오**

### **Git 저장소의 설정 파일**

Config Server가 읽을 Git 저장소에 다음과 같은 파일들이 있습니다:

```
config-repo/
├── organization-service.properties
├── organization-service-dev.properties
└── organization-service-prod.properties

```

- `organization-service-dev.properties`:
    
    ```
    spring.datasource.url=jdbc:mysql://dev-database:3306/dev_db
    spring.datasource.username=dev_user
    spring.datasource.password=dev_password
    
    ```
    

### **Config Client 동작**

1. `organization-service` 애플리케이션이 `dev` 프로파일로 실행됩니다.
2. Config Client가 Config Server에 요청:
    
    ```
    GET http://configserver:8071/organization-service/dev
    
    ```
    
3. Config Server가 `organization-service-dev.properties`의 내용을 반환.
4. Config Client는 반환받은 설정을 애플리케이션에 반영.

---

### **결론**

**Config Client**는 Config Server와 연결하여 필요한 설정을 동적으로 가져오는 역할을 합니다.

이를 통해 설정 파일을 중앙화하고, 환경별 설정 관리와 동적 변경이 가능해져 마이크로서비스의 설정 관리가 간편해집니다.