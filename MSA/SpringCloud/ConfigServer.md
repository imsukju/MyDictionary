### **Config Server란?**

Spring Cloud Config Server는 **마이크로서비스 아키텍처**에서 각 서비스의 설정 파일을 **중앙화**하여 관리하고, 클라이언트 애플리케이션이 필요한 설정을 동적으로 가져갈 수 있도록 해주는 Spring Cloud의 구성 요소입니다.

---

### **Config Server의 주요 역할**

1. **설정 중앙화**:
    - 모든 마이크로서비스의 설정 파일을 Config Server에서 관리.
    - 개별 애플리케이션마다 설정 파일을 배포할 필요 없이 Config Server에서 관리하고 제공.
2. **환경별 설정 관리**:
    - 개발(`dev`), 테스트(`qa`), 운영(`prod`) 환경별로 다른 설정 파일을 관리.
    - 클라이언트 애플리케이션은 `spring.profiles.active`를 통해 원하는 환경 설정을 요청.
3. **동적 구성 변경**:
    - Config Server와 Spring Cloud Bus를 함께 사용하면, **실시간으로 설정 변경 사항을 반영** 가능.
    - 예: 데이터베이스 연결 정보를 변경해도 클라이언트를 재배포할 필요 없이 변경 사항 적용.
4. **다양한 저장소 지원**:
    - Git, 파일 시스템, SVN, JDBC 등 다양한 저장소에서 설정 파일을 관리.

---

### **Config Server의 구성 요소**

### 1. **구성 저장소**

Config Server는 설정 파일을 **외부 저장소**(Git, 파일 시스템 등)에 저장하고 이를 클라이언트에게 제공합니다.

- **Git 저장소 예시**:
    
    ```
    config-repo/
    ├── application.properties
    ├── application-dev.properties
    ├── service-a.properties
    └── service-b.properties
    
    ```
    
- **파일 내용 예시** (`application-dev.properties`):
    
    ```
    spring.datasource.url=jdbc:mysql://dev-database:3306/dev_db
    spring.datasource.username=dev_user
    spring.datasource.password=dev_password
    
    ```
    

### 2. **Config Server**

Config Server는 클라이언트 애플리케이션의 요청을 받아 저장소에서 설정 파일을 읽고 이를 반환하는 역할을 합니다.

- 요청 형식:
    
    ```
    http://configserver:port/{application}/{profile}/{label}
    
    ```
    
    - `{application}`: 클라이언트 애플리케이션 이름.
    - `{profile}`: 환경 프로파일 (예: `dev`, `prod`).
    - `{label}`: Git 브랜치 또는 태그 (기본값: `main`).

### 3. **클라이언트 애플리케이션**

클라이언트 애플리케이션은 Config Server와 연결하여 필요한 설정을 가져옵니다.

- 클라이언트가 Config Server에서 설정을 가져오기 위한 설정 예:
    
    ```yaml
    spring:
      application:
        name: service-a
      cloud:
        config:
          uri: http://configserver:8071
    
    ```
    

---

### **Config Server 동작 과정**

### **1. Config Server 초기화**

- Config Server는 `@EnableConfigServer` 애너테이션을 통해 활성화.
- `application.yml`에 저장소 경로를 설정.

예시:

```yaml
server:
  port: 8071

spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/example/config-repo
          default-label: main

```

### **2. 클라이언트 요청**

- 클라이언트는 `spring.cloud.config.uri`를 통해 Config Server에 연결.
- 요청 URL 예:
    
    ```
    http://configserver:8071/service-a/dev
    
    ```
    

### **3. Config Server 응답**

- Config Server는 `service-a-dev.properties` 파일 내용을 읽고 클라이언트에 반환.
- 반환된 내용은 클라이언트 애플리케이션의 `Environment`에 추가되어 설정값으로 사용.

---

### **Config Server의 장점**

### **1. 설정 파일의 중앙화**

- 모든 마이크로서비스의 설정 파일을 한 곳에서 관리.
- 서비스별, 환경별 설정을 쉽게 추가하거나 변경 가능.

### **2. 환경별 설정**

- 프로파일(`dev`, `prod`, `test`)별로 설정을 구분하여 관리.
- 클라이언트 애플리케이션은 활성화된 프로파일에 따라 적절한 설정을 가져옴.

### **3. 설정 변경의 유연성**

- 설정 파일을 변경하면 Config Server에서 즉시 반영.
- Spring Cloud Bus와 연동 시, 변경 사항을 마이크로서비스들에 실시간으로 적용 가능.

### **4. 다중 저장소 지원**

- Git, 파일 시스템, 데이터베이스 등 다양한 저장소를 지원.
- 팀 또는 프로젝트 요구 사항에 맞게 설정 파일 관리 가능.

### **5. 보안 및 암호화 지원**

- 민감한 정보(예: 비밀번호)를 암호화하여 저장하고, Config Server가 이를 복호화하여 제공.
- Spring Cloud Config는 기본적으로 `JCE`를 이용한 암호화를 지원.

---

### **Config Server의 요청/응답 예시**

### **1. 요청 URL**

```
GET http://configserver:8071/service-a/dev

```

### **2. Config Server가 반환하는 설정**

```json
{
  "name": "service-a",
  "profiles": ["dev"],
  "label": "main",
  "version": "a1b2c3d",
  "state": null,
  "propertySources": [
    {
      "name": "https://github.com/example/config-repo/service-a-dev.properties",
      "source": {
        "spring.datasource.url": "jdbc:mysql://dev-database:3306/dev_db",
        "spring.datasource.username": "dev_user",
        "spring.datasource.password": "dev_password"
      }
    }
  ]
}

```

---

### **Config Server 설정 예시**

### **Config Server 애플리케이션 코드**

```java
@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }
}

```

### **Config Server 설정 파일**

```yaml
server:
  port: 8071

spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/example/config-repo
          default-label: main
          search-paths:
            - service-a
            - service-b

```

---

### **Config Server의 한계와 고려사항**

1. **Config Server 가용성**:
    - Config Server가 다운되면 클라이언트가 설정을 가져올 수 없음. 고가용성을 위해 다중 인스턴스 배포 필요.
2. **보안**:
    - 암호화 및 인증/인가를 통해 민감한 정보 보호 필요.
3. **클라이언트와의 의존성**:
    - Config Server와 클라이언트 간의 통신 장애 시 설정 로드 실패 가능.

---

### **결론**

Config Server는 마이크로서비스 환경에서 설정 파일을 **중앙화**, **환경별 관리**, **동적 업데이트**할 수 있는 핵심 도구입니다. 이를 통해 설정 관리를 단순화하고, 변화하는 요구사항에 유연하게 대응할 수 있습니다.