# Spring IOC container

## Di(Dependency Injection)
- 의존성을 외부에서 주입한다
- loose coupling 를 달성하기 위한 요소이다
  
  
  
## Di의 3가지 방식
  
### 1. 생성자 주입 (Constructor argument)
생성자 주입은 객체의 생성자를 통해 필요한 의존성을 주입하는 방식입니다. 의존성을 주입받을 객체는 생성자 파라미터를 통해 의존성을 받습니다.  
**장점**
- 의존성이 주입되지 않으면 객체가 생성되지 않으므로 불완전한 상태로 객체가 사용되는 것을 방지합니다.
- 주입된 의존성이 불변(immutable)으로 설정되기 때문에 안전합니다  

**단점**
- 의존성이 많아질수록 생성자의 파라미터 목록이 길어질 수 있습니다.
```java
public class Service {
    private final Repository repository;

    public Service(Repository repository) {
        this.repository = repository;
    }

    public void performAction() {
        repository.action();
    }
}
```
  
### 2. 세터 주입 (method argument)
세터 주입은 객체의 세터 메서드를 통해 의존성을 주입하는 방식입니다. 객체가 생성된 후, 필요에 따라 의존성을 설정할 수 있습니다  
**장점**
- 의존성을 선택적으로 설정할 수 있어 유연합니다.
- 객체를 먼저 생성하고 의존성을 나중에 주입할 수 있습니다.
**단점**
- 객체가 불완전한 상태로 사용될 가능성이 있습니다.
- 불변성을 보장할 수 없습니다.
 ```java
public class Service {
    private Repository repository;

    public void setRepository(Repository repository) {
        this.repository = repository;
    }

    public void performAction() {
        repository.action();
    }
}
``` 
  
### 3. 인터페이스 주입 (Interface Injection)
인터페이스 주입은 의존성을 주입받는 객체가 특정 인터페이스를 구현하여, 의존성을 설정하는 메서드를 제공하는 방식입니다. DI 프레임워크가 의존성을 주입할 때 이 메서드를 호출합니다.    
**장점**
- 객체가 의존성을 필요로 한다는 것을 명확히 표현할 수 있습니다.
**단점**
- 클래스가 특정 DI 프레임워크에 종속될 수 있습니다.
- 구현이 복잡할 수 있습니다. 
```java
public interface Injectable {
    void injectDependency(Dependency dependency);
}

public class Service implements Injectable {
    private Dependency dependency;

    @Override
    public void injectDependency(Dependency dependency) {
        this.dependency = dependency;
    }

    public void performAction() {
        dependency.action();
    }
}
```
   
   
### Di를 활용하지 않은 예제
```java
// Repository.java
public class Repository {
    public void saveData(String data) {
        System.out.println("Data saved: " + data);
    }
}

// Service.java
public class Service {
    private Repository repository;

    public Service() {
        // 직접 의존성을 생성
        this.repository = new Repository();
    }

    public void performService(String data) {
        repository.saveData(data);
    }
}

// Main.java
public class Main {
    public static void main(String[] args) {
        Service service = new Service();
        service.performService("example data");
    }
}
```
**Service 클래스에서 직접 의존성을 생성**

결합도가 높아서 문제가 발생할 수 있는 코드이다.
Repository 클래스를 코드를 수정한다면 Service 클래스의 코드도 수정해야 하며, 코드의 중복이 발생하여 유지보수가 어려워 진다

### Di를 활용
```java
// Repository.java
public class Repository {
    public void saveData(String data) {
        System.out.println("Data saved: " + data);
    }
}

// Service.java
public class Service {
    private Repository repository;

    // 생성자를 통해 의존성 주입
    public Service(Repository repository) {
        this.repository = repository;
    }

    public void performService(String data) {
        repository.saveData(data);
    }
}

// Main.java
public class Main {
    public static void main(String[] args) {
        // 의존성 주입
        Repository repository = new Repository();
        Service service = new Service(repository);
        service.performService("example data");
    }
}
```

**main클래스(client) 가 의존성을 주입(Injection)**  
Repository 구현체를 쉽게 수정할 수 있고 재사용성을 높일 수 있습니다.
객체를 생성하는 제어권을 main 에게 빼앗김(제어의 역전)

## The IoC Container

### IOC(제어의 역전)란?
객체의 제어 권한을 개발자가 아닌 프레임 워크나 컨테이너가 담당하는 디자인 패턴을 말합니다. 이를 통해 객체 간의 결합도를 낮추고 유연성을 높일 수 있습니다.


### IOC 컨테이너
IOC 개념을 구현한 프레임워크의 구성 요소로, 객체의 생명주기를 관리하고 의존성을 주입하는 역활을 합니다.  
스프링 프레임워크에서는 주로 ApplicationContext와 BeanFactory가 이 역할을 수행합니다
ioc 컨테이너는 빈을 생성하 떼 이러한 의존성을 주입합니다.
빈 자체가 클래스의 직접 생성 또는 서비스 로케이터 패턴과 같은 메커니즘을 사용하여 자신의 의존성을 인스턴스화하거나 위치를 제어하는 것의 반대
> 의존성 주입은 런타임때 실행함

### 1.BeanFactory
스프링의 기본 IOC 컨테이너 인터페이스로, 기본적인 DI 기능을 제공합니다.  
주로 리소스가 제한된 환경에서 사용됩니다. 'BeanFactory'는 지연 로딩(lazy loading)을 지원하여, 필요한 시점에만 빈을 생성합니다

>BeanFactory의 구현체:
>XmlBeanFactory: XML 파일을 통해 빈을 정의하고 관리합니다. (현재는 사용이 권장되지 않습니다)

### 2.ApplicationContext
'ApplicationContext'는 'BeanFactory'를 확장한 인터페이스로, 보다 많은 엔터프라이즈 기능을 제공합니다. 'ApplicationContext'는 빈의 생명 주기를 관리하며, 이벤트 전달, 메시지 리소스 처리, AOP 통합 등의 기능을 제공합니다. 'ApplicationContext'는 즉시 로딩(eager loading)을 지원하여, 컨테이너가 초기화되는 시점에 모든 싱글톤 빈을 미리 생성합니다.
ApplicationContext는 BeanFactory의 하위 인터페이스입니다
ApplicationContext 를 구현한 구체가  IoC 컨테이너  

ApplicationContext는 다음을 추가합니다
- Spring의 AOP 기능과의 더 쉬운 통합
- 메시지 리소스 처리 (국제화에 사용)
- 이벤트 발행
- 웹 애플리케이션에서 사용하기 위한 WebApplicationContext와 같은 애플리케이션 계층별 컨텍스트.

요약하자면, BeanFactory는 구성 프레임워크와 기본 기능을 제공하며, ApplicationContext는 더 많은 엔터프라이즈 관련 기능을 추가합니다. ApplicationContext는 BeanFactory의 완전한 상위 집합으로, 이 장에서는 Spring의 IoC 컨테이너 설명에 독점적으로 사용됩니다

Spring에서 애플리케이션의 중추를 형성하고 Spring IoC 컨테이너에 의해 관리되는 객체를 빈(Bean)이라고 합니다.  
 빈은 Spring IoC 컨테이너에 의해 인스턴스화되고, 조립되고, 관리되는 객체입니다. 
 그렇지 않으면 빈은 애플리케이션의 많은 객체 중 하나일 뿐입니다.  
 빈과 그들 사이의 의존성은 컨테이너에서 사용되는 구성 메타데이터에 반영됩니다.

> 여기서 조립이란 Dependency Injection 를 의미합니다
빈에게 필요한 Dependency를 IOC 컨테이너가 직접 주입합니다

### IOC 구현방법 예시

1. XML 기반 설정 : XML 파일을 사용하여 빈과 의존성을 설정하는 방법입니다.  **현재는 잘 사용하지 않음**
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="myRepository" class="com.example.MyRepository" />
    <bean id="myService" class="com.example.MyService">
        <property name="repository" ref="myRepository" />
    </bean>

</beans>
```
```java
ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
MyService myService = context.getBean(MyService.class);
```

2. 애너테이션 기반 설정
애너테이션을 사용하여 빈과 의존성을 설정하는 방법입니다.

MyRepository.java:
```java
@Repository
public class MyRepository {
    // ...
}

```

MyService.java:
```java
@Service
public class MyService {
    private final MyRepository repository;

    @Autowired
    public MyService(MyRepository repository) {
        this.repository = repository;
    }
    // ...
}

```


3. 자바 기반 설정
자바 클래스를 사용하여 빈과 의존성을 설정하는 방법입니다.

AppConfig.java:
```java
@Configuration
public class AppConfig {

    @Bean
    public MyRepository myRepository() {
        return new MyRepository();
    }

    @Bean
    public MyService myService() {
        return new MyService(myRepository());
    }
}

```

```java
public class MainApplication {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        MyService myService = context.getBean(MyService.class);
    }
}
```
