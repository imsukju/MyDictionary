### 1. **생성자 주입 (Constructor Injection)**

-   **설명**: 생성자를 통해 의존성을 주입하는 가장 권장되는 방식입니다.
-   **예시**:
-   @Component public class MyService { private final MyRepository repository; public MyService(MyRepository repository) { this.repository = repository; } }

### 2. **세터 주입 (Setter Injection)**

-   **설명**: 세터 메서드를 통해 의존성을 주입하는 방식입니다.
-   **예시**:
-   @Component public class MyService { private MyRepository repository; @Autowired public void setRepository(MyRepository repository) { this.repository = repository; } }

### 3. **필드 주입 (Field Injection)**

-   **설명**: 필드에 직접 @Autowired 어노테이션을 사용하여 의존성을 주입하는 방식입니다.
-   **예시**:
    
    ```
    @Component
    public class MyService {
        @Autowired
        private MyRepository repository;
    }
    ```
    

### 4. **메서드 주입 (Method Injection)**

-   **설명**: 특정 메서드의 파라미터를 통해 의존성을 주입하는 방식입니다.
-   **예시**:
-   @Component public class MyService { private MyRepository repository; @Autowired public void initialize(MyRepository repository) { this.repository = repository; } }

### 5. **Java 기반 어노테이션 구성 (Java-based Configuration)**

-   **설명**: @Configuration과 @Bean 어노테이션을 사용하여 빈을 정의하고 의존성을 주입하는 방식입니다.
-   **예시**:
-   @Configuration public class AppConfig { @Bean public MyService myService(MyRepository repository) { return new MyService(repository); } @Bean public MyRepository myRepository() { return new MyRepositoryImpl(); } }

### 6. **XML 기반 구성 (XML-based Configuration)**

-   **설명**: XML 파일을 사용하여 빈을 정의하고 의존성을 주입하는 방식입니다.
-   **예시**:
    
    ```
    <beans>
        <bean id="myRepository" class="com.example.MyRepositoryImpl" />
        <bean id="myService" class="com.example.MyService">
            <constructor-arg ref="myRepository" />
        </bean>
    </beans>
    ```
    

### 7. **Qualifiers와 Primary**

-   **설명**: @Qualifier와 @Primary 어노테이션을 사용하여 특정 빈을 선택적으로 주입하는 방식입니다.
-   **예시**:
-   @Component public class MyService { private final MyRepository repository; @Autowired public MyService(@Qualifier("specificRepository") MyRepository repository) { this.repository = repository; } }

### 8. **Lookup 메서드 주입 (Lookup Method Injection)**

-   **설명**: 추상 메서드에 @Lookup 어노테이션을 붙여 런타임에 주입할 빈을 반환하는 방식입니다. 주로 프로토타입 빈을 싱글톤 빈에 주입할 때 사용됩니다.
-   **예시**:
-   @Component public abstract class MyService { public void performTask() { MyPrototypeBean prototypeBean = createPrototypeBean(); prototypeBean.doSomething(); } @Lookup protected abstract MyPrototypeBean createPrototypeBean(); }

### 9. **JNDI 주입 (JNDI Injection)**

-   **설명**: JNDI(Java Naming and Directory Interface)를 통해 의존성을 주입받는 방식입니다. 주로 애플리케이션 서버 환경에서 리소스를 주입할 때 사용됩니다.
-   **예시**:
-   @Component public class MyService { @Resource(name = "jdbc/myDataSource") private DataSource dataSource; // Use the dataSource }

### 10. **환경 변수 주입 (Environment Injection)**

-   **설명**: @Value 어노테이션을 사용하여 프로퍼티 파일, 시스템 환경 변수, 스프링의 Environment 인터페이스 등을 통해 값을 주입하는 방식입니다.
-   **예시**:
-   @Component public class MyService { @Value("${my.property}") private String myProperty; // Use the myProperty }

### 11. **애노테이션 기반 주입 (Annotation-based Injection)**

-   **설명**: @Autowired 외에도 @Inject (JSR-330)와 @Resource (JSR-250) 어노테이션을 사용하여 의존성을 주입할 수 있습니다. @Inject는 @Autowired와 거의 동일하며, @Resource는 JNDI와 Java EE 스타일의 의존성 주입에 주로 사용됩니다.
-   **예시**:
-   @Component public class MyService { @Inject private MyRepository repository; @Resource(name = "anotherBean") private AnotherBean anotherBean; }

### 12. **스프링 표현식 언어 (SpEL, Spring Expression Language) 주입**

-   **설명**: @Value 어노테이션과 SpEL을 사용하여 표현식 기반의 값을 주입하는 방식입니다. 복잡한 표현식이나 연산이 필요한 경우 유용합니다.
-   **예시**:
-   @Component public class MyService { @Value("#{systemProperties\['user.name'\]}") private String userName; @Value("#{myBean.someProperty + 10}") private int calculatedValue; }

### 13. **프로파일 기반 주입 (Profile-based Injection)**

-   **설명**: @Profile 어노테이션을 사용하여 특정 환경에서만 빈을 주입하거나 활성화하는 방식입니다. 개발, 테스트, 프로덕션 환경별로 다른 빈을 사용해야 할 때 유용합니다.
-   **예시**:
-   @Profile("dev") @Bean public MyService devMyService() { return new DevMyServiceImpl(); } @Profile("prod") @Bean public MyService prodMyService() { return new ProdMyServiceImpl(); }

### 14. **지연 주입 (Lazy Injection)**

-   **설명**: @Lazy 어노테이션을 사용하여 빈의 초기화를 지연시키는 방식입니다. 주로 빈의 생성 비용이 높거나 애플리케이션 시작 시점에서 생성할 필요가 없는 경우 사용됩니다.
-   **예시**:
-   @Component public class MyService { @Autowired @Lazy private HeavyService heavyService; }

스프링 프레임워크는 다양한 상황에 맞춰 의존성 주입을 수행할 수 있는 유연한 방법을 제공합니다. 위의 목록은 스프링에서 사용할 수 있는 의존성 주입 방식을 가능한 많이 포함하고 있으며, 각 방식은 상황과 요구 사항에 맞게 적절히 선택하여 사용할 수 있습니다.