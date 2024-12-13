### **Environment 인터페이스 개념**

Spring Framework의 **`Environment` 인터페이스**는 애플리케이션의 설정 및 환경과 관련된 프로파일(`profile`)과 프로퍼티(`properties`)를 관리하는 역할을 합니다. 이 인터페이스는 **컨테이너가 애플리케이션 환경을 제어하고 구성할 수 있는 추상화된 계층**을 제공합니다.

---

### **1. Profile**

- **Profile의 정의**:
    - 프로파일은 특정 **환경에서 활성화해야 할 빈 정의의 그룹**을 논리적으로 분리한 것입니다.
    - 예를 들어, 개발 환경에서 사용되는 빈과 운영 환경에서 사용되는 빈을 다르게 정의하여 관리할 수 있습니다.
    - Spring은 `@Profile` 어노테이션을 통해 프로파일 기반으로 빈 등록을 제어합니다.
- **Profile의 역할**:
    1. 현재 **활성화된 프로파일** 확인.
    2. **디폴트로 활성화되어야 할 프로파일** 정의.
- **Profile의 사용 이유**:
    - 애플리케이션이 서로 다른 환경(예: 개발, 테스트, 운영)에서 실행될 때 환경별로 적합한 빈 구성을 제공.
    - 이를 통해 환경에 따른 설정 파일을 선택하거나 빈 구현체를 동적으로 결정.

---

### **2. Properties**

- **Properties의 정의**:
    - Properties는 애플리케이션의 설정 값을 **키-값 쌍**으로 관리합니다.
    - 다양한 출처에서 올 수 있으며, 예를 들어:
        - **Properties 파일** (`.properties` 파일)
        - **JVM 시스템 속성** (`D` 명령줄 옵션)
        - **운영체제 환경 변수**
        - **JNDI** (Java Naming and Directory Interface)
        - **Servlet 컨텍스트 파라미터**.
- **Properties의 역할**:
    1. 다양한 **속성 소스의 우선순위**를 정의하고 관리.
    2. 특정 속성 값을 손쉽게 **검색하고 해결**할 수 있도록 함.
- **Properties의 중요성**:
    - 외부 설정 파일을 통한 유연한 설정 관리.
    - 코드를 수정하지 않고 환경별로 설정을 변경할 수 있음.

---

### **Bean Definition Profiles (빈 정의 프로파일)**

빈 정의 프로파일은 다양한 환경에서 다른 빈을 동적으로 등록하는 메커니즘을 제공합니다.

### **1. 예제 시나리오**

- **개발 환경**에서는 인메모리 데이터베이스를 사용.
- **운영 환경**에서는 JNDI를 통해 데이터 소스를 조회.

### **2. Profile 기반 데이터 소스 정의**

### **개발 환경에서의 DataSource 빈 정의**

```java
@Bean
public DataSource dataSource() {
    return new EmbeddedDatabaseBuilder()
        .setType(EmbeddedDatabaseType.HSQL)
        .addScript("my-schema.sql")
        .addScript("my-test-data.sql")
        .build();
}

```

### **운영 환경에서의 DataSource 빈 정의**

```java
@Bean(destroyMethod = "")
public DataSource dataSource() throws Exception {
    Context ctx = new InitialContext();
    return (DataSource) ctx.lookup("java:comp/env/jdbc/datasource");
}

```

### **3. Profile을 사용한 전환 방식**

과거에는 환경 변수, `XML <import>` 문, 또는 `${placeholder}` 토큰 등을 활용하여 프로파일 전환을 처리했습니다. 하지만 이는 복잡하고 유지보수가 어려웠습니다. 이를 대체하기 위해 Spring은 **`@Profile` 어노테이션**을 제공합니다.

---

### **@Profile 사용법**

**`@Profile` 어노테이션**은 특정 프로파일이 활성화될 때만 빈이 등록되도록 설정합니다.

### **1. @Profile을 사용한 데이터 소스 구성**

### **개발 환경**

```java
@Configuration
@Profile("development")
public class StandaloneDataConfig {

    @Bean
    public DataSource dataSource() {
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.HSQL)
            .addScript("classpath:com/bank/config/sql/schema.sql")
            .addScript("classpath:com/bank/config/sql/test-data.sql")
            .build();
    }
}

```

### **운영 환경**

```java
@Configuration
@Profile("production")
public class JndiDataConfig {

    @Bean(destroyMethod = "")
    public DataSource dataSource() throws Exception {
        Context ctx = new InitialContext();
        return (DataSource) ctx.lookup("java:comp/env/jdbc/datasource");
    }
}

```

---

### **2. 복합 Profile 표현식**

- Profile 표현식은 논리 연산을 통해 복잡한 조건을 정의할 수 있습니다.
- 지원 연산자:
    - `!` : NOT 연산 (프로파일이 활성화되지 않은 경우).
    - `&` : AND 연산 (두 프로파일 모두 활성화된 경우).
    - `|` : OR 연산 (둘 중 하나가 활성화된 경우).

### **예제**

```java
@Profile("production & us-east | eu-central")

```

위 표현식은 다음으로 재작성되어야 합니다:

```java
@Profile("production & (us-east | eu-central)")

```

---

### **3. 사용자 정의 Profile 어노테이션**

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Profile("production")
public @interface Production {
}

```

이제 클래스에서 직접 `@Production`을 사용하여 간소화된 방식으로 프로파일을 지정할 수 있습니다.

---

### **프로파일 활성화**

### **1. 프로그래밍 방식으로 활성화**

```java
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
ctx.getEnvironment().setActiveProfiles("development");
ctx.register(SomeConfig.class, StandaloneDataConfig.class, JndiDataConfig.class);
ctx.refresh();

```

### **2. 선언적으로 활성화**

- JVM 시스템 속성:
    
    ```bash
    -Dspring.profiles.active="profile1,profile2"
    
    ```
    
- 설정 파일에서:
    
    ```yaml
    spring:
      profiles:
        active: development
    
    ```
    

---

### **Default Profile (디폴트 프로파일)**

- **정의**:
    - 활성화된 프로파일이 없을 때 사용할 기본 프로파일.
- **디폴트 프로파일 지정 방법**:
    - 코드에서:
        
        ```java
        ctx.getEnvironment().setDefaultProfiles("default");
        
        ```
        
    - 설정 파일에서:
        
        ```yaml
        spring:
          profiles:
            default: default-profile
        
        ```
        

---

### **PropertySource Abstraction**

### **1. PropertySource의 역할**

- 다양한 소스에서 프로퍼티를 관리.
- 키-값 쌍을 통해 속성 조회.

### **2. Spring에서의 기본 PropertySource**

- JVM 시스템 속성.
- 운영체제 환경 변수.

### **3. 커스텀 PropertySource 추가**

```java
ConfigurableApplicationContext ctx = new GenericApplicationContext();
MutablePropertySources sources = ctx.getEnvironment().getPropertySources();
sources.addFirst(new MyPropertySource());

```

---

### **@PropertySource 어노테이션**

`@PropertySource`를 사용하여 외부 파일을 선언적으로 추가할 수 있습니다.

### **예제**

```java
@Configuration
@PropertySource("classpath:/com/myco/app.properties")
public class AppConfig {

    @Autowired
    Environment env;

    @Bean
    public TestBean testBean() {
        TestBean testBean = new TestBean();
        testBean.setName(env.getProperty("testbean.name"));
        return testBean;
    }
}

```

---

위 개념과 예제를 기반으로 Environment와 관련된 모든 세부 사항이 완전히 설명되었습니다. 추가적인 구현이나 개념이 필요하면 요청하세요!