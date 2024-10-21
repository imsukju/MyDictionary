### **spring.factories 파일이란?**

**spring.factories** 파일은 Spring Boot에서 다양한 자동 설정과 초기화를 정의하는 파일로, 보통 `META-INF/spring.factories` 경로에 위치합니다. 이 파일은 Spring이 애플리케이션의 시작 시 필요한 모듈과 설정을 로드하고 자동 구성하는 데 중요한 역할을 합니다. **spring.factories**는 주로 여러 모듈의 확장성 및 유연성을 제공하며, 애플리케이션이 시작될 때 특정 기능을 쉽게 추가할 수 있도록 도와줍니다.

### **spring.factories의 주요 역할**

#### 1. **자동 설정 (Auto-Configuration)**

**spring.factories** 파일에서 가장 핵심적인 역할 중 하나는 자동 설정 클래스를 등록하는 것입니다. Spring Boot는 많은 경우 개발자가 일일이 설정을 구성하지 않도록, 자동으로 설정을 적용해 줍니다. 이러한 자동 설정은 **spring.factories** 파일에 정의된 `org.springframework.boot.autoconfigure.EnableAutoConfiguration` 키에 따라 이루어집니다.

예를 들어, 데이터베이스 설정, 보안 설정, 메시징 시스템 등 Spring Boot에서 제공하는 많은 자동 설정이 이 파일을 통해 처리됩니다.

##### **구조 및 예시**

```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration
```

위의 설정은 `EnableAutoConfiguration`이 활성화되었을 때, Spring Boot가 `DataSourceAutoConfiguration`과 `HibernateJpaAutoConfiguration`을 자동으로 구성하도록 합니다. 이 파일 내에서는 자동 설정이 필요한 클래스들이 쉼표로 구분되어 나열됩니다.

#### 2. **초기화 설정 (Initializers)**
   
**spring.factories** 파일에서는 `ApplicationContextInitializer`와 같은 초기화 관련 클래스도 등록할 수 있습니다. 이를 통해 애플리케이션 컨텍스트를 초기화하거나 특정 설정을 로딩하는 작업을 수행할 수 있습니다. 초기화는 **ApplicationContext**가 로딩되기 전에 이루어지며, 애플리케이션 시작 시 특정 작업을 수행하고 싶을 때 매우 유용합니다.

##### **구조 및 예시**

```properties
org.springframework.context.ApplicationContextInitializer=\
com.example.MyCustomInitializer
```

위와 같이 설정하면, `MyCustomInitializer` 클래스가 애플리케이션이 시작되기 전에 컨텍스트를 초기화하는 역할을 합니다. 이니셜라이저는 특정 리소스나 설정을 미리 로드하고 구성하는 데 많이 사용됩니다.

#### 3. **리스너 (Listeners)**

**spring.factories** 파일에서는 이벤트 기반 애플리케이션 로직을 구현할 수 있는 **ApplicationListener** 클래스도 등록할 수 있습니다. 이를 통해 애플리케이션에서 발생하는 특정 이벤트(예: 애플리케이션 시작, 종료 등)에 대한 리스너를 쉽게 추가할 수 있습니다.

##### **구조 및 예시**

```properties
org.springframework.context.ApplicationListener=\
com.example.MyCustomApplicationListener
```

위와 같이 설정하면 `MyCustomApplicationListener`가 특정 이벤트에 반응하는 로직을 수행합니다. 예를 들어, 애플리케이션이 시작될 때 특정 로그를 남기거나, 리소스를 할당하는 등의 작업을 수행할 수 있습니다.

### **spring.factories의 구조**

**spring.factories** 파일은 **키-값 쌍**으로 구성됩니다. 각 키는 특정 유형의 기능이나 설정을 나타내며, 값은 해당 기능을 수행하는 클래스들의 목록입니다. 이 파일은 기본적으로 아래와 같은 형식으로 작성됩니다.

```properties
키1=값1,값2,값3
키2=값4,값5
```

여기서 **키**는 Spring에서 지원하는 기능이나 확장점이며, **값**은 해당 기능을 구현하는 클래스들입니다. 이 값들은 쉼표로 구분된 여러 클래스 이름으로 나열됩니다. Spring은 이 파일을 스캔하고, 정의된 클래스들을 로드하여 특정 시점에 필요한 설정을 자동으로 적용합니다.

### **스프링 팩토리 로드 메커니즘**

Spring은 애플리케이션이 실행될 때, 클래스패스에 있는 **META-INF/spring.factories** 파일을 자동으로 스캔합니다. 각 모듈이 자신의 설정을 이 파일에 정의할 수 있으며, 애플리케이션 실행 시 이러한 설정들이 결합되어 최종적으로 애플리케이션에 적용됩니다.

- **EnableAutoConfiguration**의 경우, Spring Boot는 `spring.factories` 파일에서 `org.springframework.boot.autoconfigure.EnableAutoConfiguration` 키에 대응하는 클래스들을 모두 읽어들입니다.
- 각 자동 설정 클래스는 자신이 조건을 충족할 경우에만 적용됩니다. 예를 들어, `DataSourceAutoConfiguration`은 클래스패스에 데이터베이스 관련 라이브러리가 존재할 때만 설정이 활성화됩니다.

이 과정은 Spring Boot의 **자동 설정 조건(ConditionalOnClass, ConditionalOnMissingBean)** 등과 연계되어 동작합니다. 즉, 조건이 맞는 경우에만 해당 자동 설정이 활성화되므로, 불필요한 설정이 적용되지 않도록 관리할 수 있습니다.

### **Spring Boot 버전별 차이**

Spring Boot 2.x에서는 **spring.factories** 파일을 통해 주로 자동 설정을 처리했지만, 3.x로 넘어오면서 자동 구성 방식이 조금 더 세분화되고 최적화되었습니다. 최신 버전에서는 **spring.factories**뿐만 아니라, **AOT(Ahead Of Time) Processing**와 같은 새로운 최적화 기법도 도입되었습니다. 이를 통해 네이티브 이미지(native image)나 경량화된 애플리케이션 구성을 더욱 쉽게 할 수 있게 되었지만, 여전히 **spring.factories** 파일은 중요한 구성 요소로 남아 있습니다.

### **활용 예시**

직접 **spring.factories** 파일을 수정하거나 새로 추가해보는 예시는 다음과 같습니다.

#### 1. **커스텀 자동 설정 추가**

직접 커스텀 자동 설정 클래스를 만들고 이를 **spring.factories** 파일에 추가할 수 있습니다.

```java
@Configuration
public class MyCustomAutoConfiguration {
    @Bean
    public MyService myService() {
        return new MyService();
    }
}
```

**spring.factories** 파일에 이를 등록합니다.

```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.example.MyCustomAutoConfiguration
```

이제 Spring Boot는 애플리케이션 실행 시 **MyCustomAutoConfiguration**을 자동으로 로드하고, **MyService** 빈을 생성하게 됩니다.

#### 2. **애플리케이션 이벤트 리스너 추가**

Spring Boot 애플리케이션에서 커스텀 이벤트 리스너를 추가하려면 **ApplicationListener**를 구현한 클래스를 등록할 수 있습니다.

```java
public class MyCustomApplicationListener implements ApplicationListener<ApplicationReadyEvent> {
    @Override
    public void onApplicationEvent(ApplicationReadyEvent event) {
        System.out.println("Application is ready!");
    }
}
```

**spring.factories** 파일에 이를 등록합니다.

```properties
org.springframework.context.ApplicationListener=\
com.example.MyCustomApplicationListener
```

이제 애플리케이션이 준비되면 해당 리스너가 실행되어 콘솔에 메시지가 출력됩니다.

---

### **요약**

**spring.factories** 파일은 Spring Boot에서 자동 설정과 초기화를 처리하는 중요한 구성 파일입니다. 이 파일은 자동 설정 클래스와 초기화 클래스, 리스너 등을 쉽게 등록하고 로드할 수 있도록 도와줍니다. 이를 통해 개발자는 복잡한 설정을 일일이 정의할 필요 없이, 필요한 기능을 손쉽게 확장할 수 있습니다.

이를 더 깊이 이해하기 위해서는 파일 구조, 클래스 등록 방식, 그리고 실제 코드 예시를 이해하는 것이 중요하며, 각 설정이 어떻게 자동으로 적용되는지를 알고 나면 Spring Boot 애플리케이션을 보다 효율적으로 관리할 수 있습니다.

