## Spring Bean 이란?
스프링 빈(Bean)은 스프링 IoC 컨테이너에 의해 관리되는 객체를 의미합니다. 
스프링 애플리케이션에서 빈은 애플리케이션의 구성 요소로, 컨테이너는 이러한 빈의 생성, 초기화, 의존성 관리 및 생명주기를 제어합니다. 
빈은 주로 애플리케이션의 비즈니스 로직을 구현하거나, 서비스, 데이터 액세스 객체(DAO), 컨트롤러 등의 역할을 합니다.

## Bean 의 라이프 사이클

1. 인스턴스화 (Instantiation)
    - 스프링 컨테이너가 빈의 인스턴스를 생성합니다.

2. 의존성 주입 (Dependency Injection)
    - 빈이 생성된 후, 스프링 컨테이너가 빈에 필요한 의존성을 주입합니다. 이는 생성자 주입, 세터 주입, 또는 필드 주입을 통해 이루어질 수 있습니다.

3. 초기화 (Initialization)
    - 빈의 프로퍼티가 설정된 후, 초기화 메서드가 호출됩니다. 이는 InitializingBean 인터페이스의 afterPropertiesSet 메서드 또는 빈 설정 파일에 정의된 init-method 속성을 통해 실행될 수 있습니다.

4. 사용 (Usage)
    - 빈은 애플리케이션에 의해 사용됩니다. 이 단계 동안 빈은 비즈니스 로직을 수행하고 요청을 처리합니다.

3. 소멸 (Destruction)
    - 애플리케이션이 종료되거나 빈이 더 이상 필요하지 않을 때, 스프링 컨테이너는 빈의 소멸 메서드를 호출합니다. 이는 DisposableBean 인터페이스의 destroy 메서드 또는 빈 설정 파일에 정의된 destroy-method 속성을 통해 실행될 수 있습니다.

### 예시코드

#### 예제 빈 클래스
```java
public class MyBean implements InitializingBean, DisposableBean {

    public void afterPropertiesSet() {
        System.out.println("Bean is going through initialization.");
    }

    public void destroy() {
        System.out.println("Bean will be destroyed now.");
    }
}
```

#### 빈 설정(xml 기반)
```xml
<beans>
    <bean id="myBean" class="com.example.MyBean" init-method="init" destroy-method="cleanup"/>
</beans>
```

#### 초기화 및 소멸 메서드
```java
public class MyBean {
    public void init() {
        System.out.println("Bean is going through custom initialization.");
    }

    public void cleanup() {
        System.out.println("Bean will be cleaned up now.");
    }
}
```

## 스프링에서의 프로퍼티 설정

