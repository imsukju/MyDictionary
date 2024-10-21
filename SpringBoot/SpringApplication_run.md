### SpringApplication.run()의 개요

`SpringApplication.run()`은 Spring Boot 애플리케이션의 진입점으로 사용되며, 애플리케이션을 시작하고 부트스트랩(초기화)하는 중요한 역할을 담당합니다. 이 메서드는 애플리케이션의 실행을 위한 다양한 초기화 작업을 자동으로 처리하고, 애플리케이션이 실행되기 위한 준비를 완료합니다. 그 핵심 기능은 **Spring 컨텍스트(ApplicationContext)를 생성하고**, **설정을 적용한 후 애플리케이션을 실행하는 것**입니다.

### SpringApplication.run()의 주요 기능

1. **SpringApplication 객체 생성**:
    - `SpringApplication.run()`은 내부적으로 `SpringApplication` 객체를 생성합니다. 이 객체는 애플리케이션을 부트스트랩하는데 필요한 여러 설정을 관리합니다.
    - 여기에는 애플리케이션 실행 중 발생하는 다양한 이벤트(ApplicationEvent)를 처리할 수 있는 리스너와 환경 설정, 초기화 작업이 포함됩니다.

2. **애플리케이션 유형 결정**:
    - Spring Boot는 애플리케이션이 **웹 애플리케이션**인지 **비웹 애플리케이션**인지 자동으로 감지합니다.
    - 애플리케이션의 유형에 따라 내장된 웹 서버(예: Tomcat, Jetty)를 설정하고 실행할지, 일반적인 비웹 애플리케이션으로 실행할지를 결정합니다.

3. **Spring 컨텍스트 생성**:
    - Spring Boot는 **ApplicationContext**를 생성하여, 애플리케이션에 필요한 빈(Bean)을 관리하고 설정을 적용합니다. 애플리케이션의 모든 설정과 컴포넌트가 이 컨텍스트 안에서 관리됩니다.
    - 웹 애플리케이션이라면 `AnnotationConfigServletWebServerApplicationContext`, 비웹 애플리케이션이라면 `AnnotationConfigApplicationContext`가 사용됩니다.

4. **애플리케이션 이벤트 및 리스너 처리**:
    - Spring Boot는 애플리케이션의 수명 주기 동안 발생하는 여러 **이벤트**를 처리합니다. 애플리케이션이 시작될 때와 종료될 때, 설정이 준비될 때와 같이 다양한 시점에서 이벤트가 발생합니다.
    - `ApplicationEvent`와 `ApplicationListener`를 사용해 특정 시점에 필요한 작업을 처리할 수 있습니다. 예를 들어, `ApplicationStartingEvent`, `ApplicationReadyEvent` 등이 있습니다.

5. **환경 설정 준비**:
    - `SpringApplication.run()`은 **Spring Environment**를 초기화하여, `application.properties` 또는 `application.yml` 파일을 통해 설정을 로드합니다.
    - 이 설정을 통해 개발자는 애플리케이션 실행 시 환경에 따라 다른 값을 적용할 수 있습니다(예: 개발 환경, 운영 환경).

6. **빈 등록 및 의존성 주입**:
    - `@ComponentScan`을 통해 패키지를 스캔하여 빈으로 등록된 클래스들을 Spring 컨텍스트에 추가합니다.
    - Spring DI(Dependency Injection, 의존성 주입) 메커니즘을 사용하여 등록된 빈 간의 의존성을 자동으로 해결하고 주입합니다.

7. **내장 서버 구동(웹 애플리케이션)**:
    - 애플리케이션이 웹 애플리케이션일 경우, `SpringApplication.run()`은 내장된 웹 서버(예: Tomcat, Jetty)를 자동으로 구동합니다.
    - 이를 통해 개발자는 별도의 외부 서버를 설정할 필요 없이 바로 HTTP 요청을 받을 수 있는 환경을 갖출 수 있습니다.

8. **명령어 라인 파싱**:
    - `SpringApplication.run()`은 명령어 라인에서 전달된 인자들을 파싱하여 애플리케이션 실행 시 환경을 동적으로 변경할 수 있도록 합니다.

9. **CommandLineRunner 및 ApplicationRunner 실행**:
    - 애플리케이션이 실행된 후 `CommandLineRunner`나 `ApplicationRunner`를 구현한 빈들을 찾아 실행합니다. 이를 통해 애플리케이션 초기화 직후에 원하는 작업을 처리할 수 있습니다.

### SpringApplication.run()의 내부 동작 과정

이제 `SpringApplication.run()`의 내부 동작 흐름을 순차적으로 살펴보겠습니다.

1. **애플리케이션 클래스 설정**:
    - Spring 애플리케이션을 시작할 때 가장 먼저 `@SpringBootApplication` 어노테이션이 있는 클래스가 전달됩니다. 이 클래스는 Spring Boot의 기본 설정을 포함하며, 부트스트랩의 중심 역할을 합니다.

2. **SpringApplication 객체 생성**:
    - `SpringApplication` 객체가 생성되고, 이를 통해 애플리케이션 초기화 작업이 시작됩니다. 이 객체는 애플리케이션이 웹인지 비웹인지, 어떤 설정이 필요한지 등을 파악합니다.

3. **애플리케이션 컨텍스트 준비**:
    - `SpringApplication.run()`은 **ApplicationContextInitializer**를 사용해 애플리케이션 컨텍스트를 초기화합니다. 이 과정에서 애플리케이션 설정과 관련된 작업이 이루어집니다.
    - 컨텍스트 초기화 후에는 `ApplicationContext`가 리프레시되어 빈이 로드되고 의존성 주입이 완료됩니다.

4. **환경 설정 적용**:
    - `prepareEnvironment()` 메서드를 통해 환경 설정이 준비되며, 프로파일이나 속성 파일(application.properties 또는 application.yml)에서 설정된 값이 적용됩니다.

5. **ApplicationEvent 처리**:
    - 애플리케이션 시작 시 `ApplicationStartingEvent`, `ApplicationEnvironmentPreparedEvent`와 같은 여러 이벤트가 발생하며, 이 이벤트를 처리할 리스너들이 동작합니다.

6. **ApplicationContext 새로 고침**:
    - 컨텍스트가 새로 고쳐지면서 모든 빈이 생성되고 초기화됩니다. 이 과정에서 애플리케이션 내에서 사용할 모든 컴포넌트가 준비됩니다.

7. **내장 서버 시작(웹 애플리케이션의 경우)**:
    - 웹 애플리케이션의 경우, Spring Boot는 Tomcat, Jetty 등의 내장 웹 서버를 자동으로 구동시킵니다. 이렇게 내장된 서버는 기본적으로 HTTP 요청을 처리할 준비가 됩니다.

8. **CommandLineRunner 및 ApplicationRunner 실행**:
    - `SpringApplication.run()`은 애플리케이션 실행 후 `CommandLineRunner`와 `ApplicationRunner`를 실행하여, 추가적으로 초기화 작업을 처리할 수 있도록 합니다.

9. **애플리케이션 실행 완료**:
    - 모든 초기화 작업이 끝나면 `ApplicationReadyEvent`가 발생하며, 애플리케이션이 정상적으로 시작되었음을 알립니다.

### SpringApplication.run() 코드 분석

다음은 `SpringApplication.run()`의 기본적인 코드 흐름입니다:

```java
public ConfigurableApplicationContext run(String... args) {
    long startTime = System.nanoTime();
    DefaultBootstrapContext bootstrapContext = createBootstrapContext();
    ConfigurableApplicationContext context = null;
    configureHeadlessProperty();
    SpringApplicationRunListeners listeners = getRunListeners(args);
    listeners.starting(bootstrapContext, this.mainApplicationClass);
    try {
        ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
        ConfigurableEnvironment environment = prepareEnvironment(listeners, bootstrapContext, applicationArguments);
        Banner printedBanner = printBanner(environment);
        context = createApplicationContext();
        context.setApplicationStartup(this.applicationStartup);
        prepareContext(bootstrapContext, context, environment, listeners, applicationArguments, printedBanner);
        refreshContext(context);
        afterRefresh(context, applicationArguments);
        Duration timeTakenToStartup = Duration.ofNanos(System.nanoTime() - startTime);
        if (this.logStartupInfo) {
            new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), timeTakenToStartup);
        }
        listeners.started(context, timeTakenToStartup);
        callRunners(context, applicationArguments);
    } catch (Throwable ex) {
        handleRunFailure(context, ex, listeners);
        throw new IllegalStateException(ex);
    }
    try {
        if (context.isRunning()) {
            Duration timeTakenToReady = Duration.ofNanos(System.nanoTime() - startTime);
            listeners.ready(context, timeTakenToReady);
        }
    } catch (Throwable ex) {
        handleRunFailure(context, ex, null);
        throw new IllegalStateException(ex);
    }
    return context;
}
```

### 각 단계의 설명

1. **시작 시간 기록**:
    - 애플리케이션이 시작되는 시점을 기록하여, 이후 성능 분석 시 시작 시간을 추적할 수 있습니다.

2. **BootstrapContext 생성**:
    - `DefaultBootstrapContext`는 Spring의 초기화 과정에서 필요한 설정과 리소스를 관리합니다.

3. **Headless 속성 설정**:
    - `java.awt.headless` 속성을 설정하여 서버에서 GUI 리소스를 불러오지 않도록 처리합니다.

4. **리스너 초기화 및 실행**:
    - `SpringApplicationRunListeners`는 애플리케이션 실행 중 발생하는 이벤트를 리스닝하며, 이 시점에서 애플리케이션의 상태를 추적합니다.

5. **환경 설정 준비**:
    - `prepareEnvironment()` 메서드는 환경 변수를 설정하고, 프로파일 및 설정 파일의 내용을 기반으로 애플리케이션 환경을 구성합니다.

6. **애플리케이션 컨텍스트 준비 및 새로 고침**

:
    - `prepareContext()`와 `refreshContext()` 메서드는 애플리케이션 컨텍스트를 초기화하고 모든 빈을 로드하며, 애플리케이션이 동작할 준비를 마칩니다.

7. **CommandLineRunner 및 ApplicationRunner 실행**:
    - 이 두 클래스는 애플리케이션이 시작된 후 원하는 추가 작업을 수행할 수 있도록 도와줍니다.

8. **애플리케이션 준비 완료**:
    - 모든 준비가 완료되면 `ApplicationReadyEvent`가 발생하여 애플리케이션이 성공적으로 실행되었음을 알립니다.

### 결론

`SpringApplication.run()` 메서드는 Spring Boot 애플리케이션을 실행하고 초기화하는 데 필요한 모든 과정을 자동으로 처리하여, 개발자가 복잡한 설정 없이도 신속하게 애플리케이션을 구동할 수 있도록 도와줍니다. 각종 설정 로드, 빈 관리, 이벤트 처리, 내장 서버 실행 등 다양한 작업을 수행하여 애플리케이션이 원활하게 동작할 수 있도록 보장합니다.