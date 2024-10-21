### 1. 애플리케이션 시작: @SpringBootApplication, @EnableAutoConfiguration, @AutoConfigurationPackage
Spring Boot 애플리케이션이 시작될 때 일반적으로 사용하는 `@SpringBootApplication` 애노테이션은 세 가지 중요한 애노테이션을 포함하고 있습니다. 이 애노테이션을 통해 Spring Boot 애플리케이션은 자동으로 필요한 설정을 구성합니다. 각각의 애노테이션은 중요한 역할을 합니다.

- **@SpringBootConfiguration**: Spring의 `@Configuration`을 확장한 애노테이션으로, Spring 애플리케이션에서 사용할 빈(bean)을 등록하고 구성하는 역할을 합니다. 모든 Spring 설정 클래스에 적용할 수 있으며, `@Bean` 메서드를 사용해 애플리케이션에서 사용할 객체들을 정의합니다.
  
- **@EnableAutoConfiguration**: Spring Boot의 핵심 애노테이션으로, 자동 구성 기능을 활성화합니다. 이 애노테이션은 클래스패스(classpath)에서 사용 가능한 여러 자동 구성 클래스를 탐색하고 필요한 설정을 자동으로 적용합니다. 여기에는 데이터베이스 연결, 보안 설정, 웹 서버 설정 등이 포함될 수 있습니다. 이 과정에서 `spring.factories` 파일을 참조하여 어떤 구성 클래스를 로드할지 결정합니다.
  
- **@ComponentScan**: Spring 애플리케이션에서 자동으로 빈을 탐색하고 등록하기 위한 메커니즘입니다. `@Component`, `@Service`, `@Repository`, `@Controller`와 같은 애노테이션이 붙은 클래스를 자동으로 감지하여 빈으로 등록합니다. 이로 인해 개발자는 애플리케이션 내에서 별도의 빈 등록 작업 없이 필요한 객체를 자동으로 DI(Dependency Injection) 받을 수 있습니다.

또한 **@AutoConfigurationPackage**는 자동 구성 프로세스가 애플리케이션의 패키지 범위 내에서만 동작하도록 제한하는데, 해당 패키지에서 컴포넌트 스캔을 활성화하여 빈을 자동으로 감지하고 등록합니다.

#### 추가적인 내용: 애플리케이션이 실행되면서 SpringApplication.run() 메서드를 통해 `@SpringBootApplication` 클래스가 시작되고, 이는 위에서 언급한 애노테이션들이 내부적으로 동작하여 자동 구성을 시작합니다.

### 2. 자동 구성 클래스 탐색: AutoConfigurationImportSelector
`@EnableAutoConfiguration`이 활성화되면, Spring Boot는 `AutoConfigurationImportSelector`라는 클래스에 의해 자동 구성 프로세스를 시작합니다. 이 클래스는 자동 구성에 필요한 클래스를 찾아내는 중요한 역할을 합니다.

- **클래스 로드**: `AutoConfigurationImportSelector`는 클래스패스에서 자동 구성 클래스를 찾아 로드합니다. 이 과정에서 Spring은 `spring.factories` 파일과 `AutoConfiguration.imports` 파일을 사용합니다.
  
  - **spring.factories**: 이 파일은 `META-INF` 디렉토리에 위치하며, 자동 구성을 위해 필요한 클래스 목록을 정의합니다. 예를 들어, `DataSourceAutoConfiguration`, `WebMvcAutoConfiguration`과 같은 클래스들이 여기에 포함되어 있으며, Spring Boot는 이 파일을 참조하여 필요한 자동 구성 클래스를 로드합니다.
  
  - **AutoConfiguration.imports**: Spring Boot 3.0에서 도입된 새로운 방식으로, 자동 구성 클래스를 더 효율적으로 관리합니다. 이를 통해 성능을 최적화하고 불필요한 구성을 줄이는 역할을 합니다.

- **조건부 로딩**: 자동 구성 클래스가 모두 로드되는 것은 아니며, 특정 조건에 따라 로딩됩니다. 이를 위해 Spring은 `@ConditionalOnClass`, `@ConditionalOnMissingBean`과 같은 조건 애노테이션을 사용합니다. 예를 들어, `@ConditionalOnClass`는 클래스패스에 특정 클래스가 존재할 때만 해당 구성을 적용합니다. 이 방식으로 필요한 설정만 활성화되어 애플리케이션의 성능을 최적화할 수 있습니다.

#### 추가적인 내용: `AutoConfigurationImportSelector`의 동작 과정에서는 클래스 로딩 외에도 다양한 필터링 작업이 이루어지며, 특정 환경 조건에 맞는 설정만 선택적으로 로드되도록 구성됩니다. 이 필터링 과정은 애플리케이션의 요구 사항에 맞는 최적의 설정을 제공하는 데 중요한 역할을 합니다.

### 3. 자동 구성 클래스 로드: spring.factories와 AutoConfiguration.imports
자동 구성 클래스는 `AutoConfigurationImportSelector`에 의해 결정되며, 실제로 로드되는 단계는 `spring.factories`와 `AutoConfiguration.imports` 파일에 의존합니다.

- **spring.factories**: 이 파일은 `META-INF/spring.factories` 경로에 위치하며, `EnableAutoConfiguration` 키를 기준으로 자동 구성 클래스 목록이 정의되어 있습니다. 예를 들어, 데이터베이스 자동 구성(`DataSourceAutoConfiguration`), 웹 MVC 자동 구성(`WebMvcAutoConfiguration`) 등이 포함됩니다. Spring Boot는 이 파일을 분석하여 자동 구성을 위한 클래스를 로드합니다.

- **AutoConfiguration.imports**: Spring Boot 3.0에서 도입된 최적화 방식으로, 자동 구성을 보다 효율적으로 관리합니다. 성능 향상을 위해 기존 `spring.factories`보다 간결한 방식으로 자동 구성 클래스 목록을 유지하며, 더 나은 확장성과 유연성을 제공합니다.

#### 추가적인 내용: 이 단계에서는 로드된 구성 클래스들이 애플리케이션 컨텍스트에 추가되며, 각각의 클래스는 @Configuration으로 선언되어 필요한 빈들을 애플리케이션 컨텍스트에 등록합니다.

### 4. 지연된 구성 처리: DeferredImportSelector
모든 자동 구성 클래스가 즉시 로드되지 않고, 특정 상황에서 지연 처리되어야 하는 경우가 있습니다. 이를 처리하는 것이 `DeferredImportSelector`입니다.

- **지연 처리 메커니즘**: `DeferredImportSelector`는 Spring 컨텍스트 초기화가 완료된 후, 필요할 때 추가적인 구성 클래스를 로드할 수 있도록 합니다. 예를 들어, Spring Security와 같은 복잡한 구성 요소는 초기 설정이 완료된 이후에 적용될 수 있으며, 이는 `DeferredImportSelector`에 의해 관리됩니다.
  
- **활용 시나리오**: 일반적으로 데이터베이스 연결 설정이나 보안 설정 등은 기본 애플리케이션 설정이 완료된 후에 적용됩니다. 이 지연된 구성을 통해 애플리케이션이 모든 기본 설정을 마친 후 필요한 추가 설정을 효율적으로 적용할 수 있습니다.

### 5. 웹 애플리케이션 설정: AnnotationConfigServletWebServerApplicationContext
Spring Boot가 웹 애플리케이션을 구동할 때는 내장된 웹 서버 설정이 필요합니다. Spring Boot는 이를 자동으로 처리하며, 이 과정에서 `AnnotationConfigServletWebServerApplicationContext`가 사용됩니다.

- **내장 웹 서버 설정**: Spring Boot는 기본적으로 Tomcat, Jetty, Undertow와 같은 내장 웹 서버를 제공합니다. 애플리케이션이 웹 기반일 경우, Spring Boot는 자동으로 이 웹 서버를 구성하고 구동시킵니다. 개발자는 `application.properties`나 `application.yml` 파일을 통해 포트 번호, SSL 설정 등을 쉽게 변경할 수 있습니다.

- **서블릿 컨텍스트 생성**: 웹 애플리케이션의 중심 역할을 하는 `DispatcherServlet`이 자동으로 설정되고, 이와 함께 필터와 리스너도 설정됩니다. 이로써 클라이언트의 요청을 처리할 준비가 완료됩니다.

#### 추가적인 내용: 내장 웹 서버의 동작은 Spring Boot의 주요 장점 중 하나로, 별도의 외부 서버 설정 없이도 빠르게 애플리케이션을 구동할 수 있습니다. 또한, 개발자는 이를 커스터마이징하여 서버 설정을 세부적으로 제어할 수 있습니다.

### 6. 구성 클래스 파싱: ConfigurationClassParser
자동 구성 클래스가 모두 로드된 후, Spring의 `ConfigurationClassParser`가 이들을 파싱하여 실제로 사용할 빈 정의를 추출하는 단계입니다.

- **빈 정의 추출**: `@Configuration`, `@Bean`, `@Component`, `@Service`, `@Repository`로 선언된 빈을 추출하여 Spring의 빈 팩토리(DefaultListableBeanFactory)에 등록합니다. 이를 통해 애플리케이션에서 사용할 객체들이 생성됩니다.
  
- **조건부 구성**: `@Conditional` 애노테이션을 통해 조건부로 적용될 설정들이 파싱됩니다. 예를 들어, 데이터 소스 빈이 이미 존재할 경우 `DataSourceAutoConfiguration`에서 제공하는 데이터 소스 빈이 생성되지 않도록 조건을 설정할 수 있습니다.

#### 추가적인 내용: 이 단계에서 사용되는 여러 파서와 후처리기가 Spring의 다양한 설정들을 처리하여, 자동 구성된 클래스들이 어떻게 애플리케이션 컨텍스트에 통합되는지 결정합니다.

### 7. 빈 등록: DefaultListableBeanFactory
파싱된 구성 클래스에서 추출된 빈들은 Spring의 `DefaultListableBeanFactory`에 등록됩니다. 이 빈 팩토리는 애플리케이션에서 모든 빈의 생명 주기를 관리합니다.

- **빈 관리**: `DefaultListableBeanFactory`는 빈의 생성, 초기화, 의존

성 주입, 소멸 등을 관리합니다. 여기에는 자동 구성된 빈들뿐만 아니라 개발자가 직접 정의한 빈들도 포함됩니다. 예를 들어, 자동 구성된 `DataSource`, `EntityManagerFactory`, `MessageConverter`와 같은 빈이 여기에 등록됩니다.

#### 추가적인 내용: 빈 팩토리는 다양한 후처리기(BeanPostProcessor)를 통해 빈의 초기화 작업을 처리하며, 애플리케이션에서 모든 빈들이 원활하게 동작할 수 있도록 지원합니다.

### 8. 후처리 작업: PostProcessorRegistrationDelegate, ConfigurationClassPostProcessor
모든 빈이 등록된 후에는 후처리 단계가 진행됩니다. 이 단계에서 Spring Boot는 빈의 생명 주기를 최적화하고 추가적인 설정을 적용합니다.

- **PostProcessorRegistrationDelegate**: 이 클래스는 여러 후처리기(BeanFactoryPostProcessor, BeanPostProcessor)를 등록하여 빈이 등록된 후의 작업을 처리합니다. 이를 통해 빈이 완전히 준비된 상태에서 추가적인 설정이나 후처리가 가능합니다.

- **ConfigurationClassPostProcessor**: 이 클래스는 `@Configuration` 클래스를 처리하고, 추가 빈 정의를 등록합니다. 이 후처리 과정에서 설정 클래스에서 필요한 추가 빈들이 등록되며, 애플리케이션 컨텍스트에 반영됩니다.

#### 추가적인 내용: 이 후처리 단계는 특히 AOP(Aspect-Oriented Programming)나 Spring Security와 같은 복잡한 빈 설정에서 중요한 역할을 합니다. 후처리기를 통해 빈의 상태를 조정하고, 필요한 동작을 추가할 수 있습니다.

### 9. 애플리케이션 실행
모든 자동 구성과 후처리 작업이 완료되면, 애플리케이션은 실제로 실행됩니다. 이 시점에서 웹 애플리케이션은 클라이언트의 요청을 처리할 준비를 마치고, 데이터베이스 연결, 보안 설정 등도 완료된 상태입니다.

- **웹 애플리케이션 구동**: 웹 기반 애플리케이션의 경우, 내장 웹 서버가 구동되어 HTTP 요청을 받을 준비를 마칩니다. 이때 각종 요청 처리 로직, 보안 설정 등이 자동으로 적용됩니다.

- **비즈니스 로직 동작**: 자동 구성된 빈과 개발자가 작성한 빈들이 애플리케이션에서 동작하게 되며, 개발자는 이 단계에서 별도의 설정 없이 비즈니스 로직에 집중할 수 있습니다.

#### 추가적인 내용: 자동 구성이 완료된 후에도 개발자는 특정 자동 구성을 제외하거나, 필요에 따라 추가적인 구성을 덧붙일 수 있습니다. 이를 위해 `@EnableAutoConfiguration(exclude = ...)`와 같은 방식을 사용하거나, `application.properties`에서 자동 구성 클래스를 비활성화할 수 있습니다.
