### Instantiation Beans

Bean Definition은 본질적으로 하나 이상의 객체를 생성하기 위한 **레시피**입니다. 스프링 컨테이너는 빈 객체 생성 요청 시, 명명된 빈의 레시피를[^1] 참고하여 해당 빈 정의에 포함된 구성 메타데이터를 기반으로 실제 객체를 생성하거나 가져옵니다.

> **구성 메타데이터란?**
> 
> "해당 빈 정의에 캡슐화된 구성 메타데이터"는 빈을 생성하는 데 필요한 모든 정보를 담고 있습니다. 이 정보는 스프링의 **BeanDefinition** 객체의 속성들에 저장됩니다.
>
> **BeanDefinition 객체의 주요 속성**
> - **빈 클래스 이름 (Bean Class Name):** 생성할 빈이 어떤 클래스의 인스턴스인지를 정의합니다. 스프링 컨테이너는 이 정보를 사용해 인스턴스를 생성합니다.
> - **스코프 (Scope):** 빈의 범위를 정의합니다. 예를 들어, **singleton** 스코프는 애플리케이션 컨텍스트 내에서 빈의 단일 인스턴스를, **prototype** 스코프는 요청할 때마다 새로운 인스턴스를 생성합니다.
> - **생성자 인자 값 (Constructor Arguments):** 빈을 생성할 때 생성자에 전달될 인자들을 정의합니다. 이를 통해 스프링은 올바른 생성자를 호출하여 객체를 생성합니다.
> - **프로퍼티 값 (Property Values):** 생성된 빈의 속성 값 설정에 사용됩니다. 이는 setter 메서드나 필드에 값을 주입하는 데 활용됩니다.
> - **의존성 (Dependencies):** 빈이 생성될 때 필요한 다른 빈에 대한 의존성을 정의합니다. 스프링은 이 정보를 통해 필요한 다른 빈들을 먼저 생성하고 주입합니다.
> - **초기화 메서드 (Init Method):** 빈이 생성된 후 호출될 초기화 메서드를 정의합니다.
> - **소멸 메서드 (Destroy Method):** 빈이 컨테이너에서 제거될 때 호출될 메서드를 정의합니다.
>
> **BeanDefinition 메타데이터 예시**
>
> ```java
> @Configuration
> public class AppConfig {
> 
>     @Bean(initMethod = "init", destroyMethod = "destroy")
>     public MyService myService() {
>         MyService myService = new MyService("Hello, Spring!");
>         myService.setDependency(dependencyBean());
>         return myService;
>     }
> 
>     @Bean
>     public DependencyBean dependencyBean() {
>         return new DependencyBean();
>     }
> }​
> ```
>
> 위 예시에서, **myService** 빈은 다음과 같은 메타데이터를 가집니다:
> - **빈 클래스 이름:** MyService
> - **스코프:** singleton (기본값)
> - **생성자 인자 값:** 없음 (기본 생성자 사용)
> - **프로퍼티 값:** `setDependency` 메서드를 통해 주입된 DependencyBean
> - **초기화 메서드:** init
> - **소멸 메서드:** destroy
> - **의존성:** dependencyBean
> 
> 이 모든 정보는 **BeanDefinition** 객체의 속성으로 캡슐화되어 있으며, 스프링 컨테이너는 이 정보를 바탕으로 실제 빈 객체를 생성하고 관리합니다.

XML 기반의 구성 메타데이터를 사용하는 경우, 객체의 타입(또는 클래스)을 **<bean/>** 엘리먼트의 **class** 속성에 지정합니다. 이 속성은 빈을 생성하는 데 필수적인 정보입니다. (예외 사항에 대해서는 [Instantiation by Using an Instance Factory Method](https://docs.spring.io/spring-framework/reference/core/beans/definition.html#beans-factory-class-instance-factory-method) 및 [Bean Definition Inheritance](https://docs.spring.io/spring-framework/reference/core/beans/child-bean-definitions.html)를 참조하십시오.) 

빈 클래스의 **class** 속성은 다음 두 가지 방식으로 사용될 수 있습니다:
- 컨테이너가 생성자를 반사적으로 호출하여 빈을 생성할 때, 이 속성은 new 연산자를 사용하는 Java 코드와 유사하게 동작합니다. 
- 특정 클래스의 static 팩토리 메서드를 호출하여 빈을 생성하는 경우, 이 속성은 실제 클래스를 지정하는 데 사용됩니다. 이 메서드는 동일한 클래스나 완전히 다른 클래스의 객체를 반환할 수 있습니다.

```java
// MyService.java
public class MyService {

    private String message;

    // private 생성자
    private MyService(String message) {
        this.message = message;
    }

    // static 팩토리 메서드
    public static MyService createInstance() {
        return new MyService("Hello from MyService!");
    }

    public String getMessage() {
        return message;
    }
}

// AppConfig.java (스프링 설정 클래스)
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public MyService myService() {
        return MyService.createInstance();
    }
}
```

> **Nested class names**  
> 중첩 클래스에 대한 빈 정의를 구성하려면, 중첩 클래스의 바이너리 이름이나 소스 이름을 사용할 수 있습니다. 예를 들어, `com.example` 패키지에 `SomeThing` 클래스가 있고, 이 클래스에 `OtherThing`이라는 정적 중첩 클래스가 있는 경우, 이들은 달러 기호($) 또는 점(.)으로 구분할 수 있습니다. 따라서 빈 정의의 class 속성 값은 `com.example.SomeThing$OtherThing` 또는 `com.example.SomeThing.OtherThing`이 됩니다.

### Instantiation with a Constructor

1. **스프링 IoC 컨테이너의 범용성**  
   스프링 IoC 컨테이너는 대부분의 일반적인 클래스를 관리할 수 있으며, 클래스를 빈으로 등록하기 위해 특별한 인터페이스를 구현하거나 특정 코딩 패턴을 따를 필요가 없습니다.

2. **생성자 접근 방식**  
   스프링은 클래스를 빈으로 만들 때 생성자를 통해 객체를 생성할 수 있습니다. 일반적으로 기본 생성자가 필요하지 않지만, 특정 IoC 방식에서는 기본 생성자가 필요할 수 있습니다.

3. **JavaBean 스타일 클래스**  
   많은 스프링 사용자는 디폴트 생성자와 설정자(setter), 접근자(getter) 메서드를 가진 JavaBean 스타일의 클래스를 선호합니다. 이는 클래스의 속성들을 설정하고 가져올 수 있는 메서드가 있는 것을 의미합니다.  
  
4. **특이한 클래스 관리**
    non-bean 스타일 클래스: 스프링 컨테이너는 일반적인 JavaBean 스타일이 아닌 특이한(non-bean) 스타일의 클래스도 관리할 수 있습니다. 예를 들어, 레거시 시스템에서 사용되는 특정 클래스를 스프링 컨테이너가 관리할 수 있습니다.   


### 1. java 스타일 클래스 예시코드   
   
```java
public class MyBean {
    private String name;
    private int age;

    // 디폴트 생성자
    public MyBean() {
    }

    // 접근자와 설정자 (Getter와 Setter)
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```   
>위 클래스는 특정 프레임워크에 종속되지 않는, 일반적인 자바 객체이므로 POJO에 해당합니다.   

**메타데이터**
```java
@Configuration
public class AppConfig {

    @Bean
    public MyBean myBean() {
        MyBean myBean = new MyBean();
        myBean.setName("Gildong");
        myBean.setAge(30);
        return myBean;
    }
}
```   
### 2. JavaBean 사양을 따르지 않는 클래스 관리 예시

```java
// JavaBean 사양을 따르지 않는 클래스
public class LegacyConnectionPool {
    private String connectionString;

    // 디폴트 생성자가 없음
    public LegacyConnectionPool(String connectionString) {
        this.connectionString = connectionString;
    }

    // JavaBean 스타일의 접근자와 설정자가 없음
    public void connect() {
        System.out.println("Connecting to: " + connectionString);
    }
}
```

```java
// AppConfig.java (스프링 설정 클래스)
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public LegacyConnectionPool legacyConnectionPool() {
        return new LegacyConnectionPool("jdbc:legacy-db://localhost:3306/mydb");
    }
}
```


### 3. Instantiation with a Static Factory Method   
```java
public class ClientService {
	private static ClientService clientService = new ClientService();
	private ClientService() {}

	public static ClientService createInstance() {
		return clientService;
	}
}
```   

```java   
@Configuration
public class AppConfig {

    @Bean
    public ClientService clientService() {
        return ClientService.createInstance();
    }
}
```   

### 4. Instantiation by Using an Instance Factory Method
```java
public interface ClientService {
    void doSomething();
}

public class ClientServiceImpl implements ClientService {
    @Override
    public void doSomething() {
        System.out.println("ClientService is doing something");
    }
}
```    

```java
@Configuration
public class AppConfig {

    @Bean
    public DefaultServiceLocator serviceLocator() {
        return new DefaultServiceLocator();
    }

    @Bean
    public ClientService clientService() {
        return serviceLocator().createClientServiceInstance();
    }
}
```   

```java
public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        ClientService clientService = context.getBean(ClientService.class);
        // ClientService 사용
        clientService.doSomething();
    }
}
```   
> 하나의 팩토리 빈 클래스가 여러 팩토리 메서드를 가질 수도 있습니다

```java
public interface AccountService {
    void performAction();
}

public class AccountServiceImpl implements AccountService {
    @Override
    public void performAction() {
        System.out.println("AccountService is performing an action");
    }
}
```   

```java
public class DefaultServiceLocator {

	private static ClientService clientService = new ClientServiceImpl();

	private static AccountService accountService = new AccountServiceImpl();

	public ClientService createClientServiceInstance() {
		return clientService;
	}

	public AccountService createAccountServiceInstance() {
		return accountService;
	}
}
```   

```java
@Configuration
public class AppConfig {

    @Bean
    public DefaultServiceLocator serviceLocator() {
        return new DefaultServiceLocator();
    }

    @Bean
    public ClientService clientService() {
        return serviceLocator().createClientServiceInstance();
    }

    @Bean
    public AccountService accountService() {
        return serviceLocator().createAccountServiceInstance();
    }
}
```   
```java
public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        ClientService clientService = context.getBean(ClientService.class);
        AccountService accountService = context.getBean(AccountService.class);

        // ClientService 및 AccountService 사용
        clientService.doSomething();
        accountService.performAction();
    }
}
```   

Spring에서 **`FactoryBean`** 인터페이스는 특별한 유형의 빈을 생성할 때 사용되는 강력한 메커니즘입니다. 이 인터페이스를 구현하면 스프링 컨테이너는 `FactoryBean`을 통해 생성된 객체를 빈으로 관리하며, 개발자는 객체 생성 로직을 커스터마이즈할 수 있습니다.

### 주요 개념

- **`FactoryBean` 인터페이스**: `FactoryBean<T>` 인터페이스는 세 가지 주요 메서드를 제공합니다.
  1. **`T getObject()`**: 이 메서드는 `FactoryBean`이 생성하는 실제 객체를 반환합니다.
  2. **`Class<?> getObjectType()`**: 이 메서드는 `getObject()` 메서드가 반환하는 객체의 타입을 반환합니다.
  3. **`boolean isSingleton()`**: 이 메서드는 `FactoryBean`이 생성하는 객체가 싱글톤인지 여부를 나타냅니다.

### 예제

아래는 간단한 `FactoryBean` 구현 예제입니다.

#### `MyBean` 클래스
```java
public class MyBean {
    private String name;

    public MyBean(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

#### `MyBeanFactory` 클래스 (`FactoryBean` 구현)
```java
import org.springframework.beans.factory.FactoryBean;

public class MyBeanFactory implements FactoryBean<MyBean> {

    private String name;

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public MyBean getObject() throws Exception {
        // MyBean 인스턴스를 생성
        return new MyBean(name);
    }

    @Override
    public Class<?> getObjectType() {
        return MyBean.class;
    }

    @Override
    public boolean isSingleton() {
        return true; // MyBean 인스턴스가 싱글톤인지 여부
    }
}
```

#### Spring 설정 (Java Config)
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public MyBeanFactory myBeanFactory() {
        MyBeanFactory factory = new MyBeanFactory();
        factory.setName("Factory Bean Example");
        return factory;
    }

    @Bean
    public MyBean myBean() throws Exception {
        return myBeanFactory().getObject();
    }
}
```

#### 사용 예시 (`Main` 클래스)
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Main {
    public static void main(String[] args) throws Exception {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        MyBean myBean = (MyBean) context.getBean("myBean");
        System.out.println("MyBean Name: " + myBean.getName());

        // FactoryBean 자체를 가져오기
        MyBeanFactory factoryBean = (MyBeanFactory) context.getBean("&myBeanFactory");
        System.out.println("FactoryBean Class: " + factoryBean.getClass().getName());
    }
}
```

### 요약

- **`FactoryBean`**은 스프링에서 특정 객체를 생성하는 특별한 방법을 제공합니다.
- `getBean("beanName")`은 `FactoryBean`이 생성한 실제 객체를 반환하고, `getBean("&beanName")`은 `FactoryBean` 자체를 반환합니다.
- `FactoryBean`을 사용하면 복잡한 객체 생성 로직을 캡슐화하고, 스프링 컨테이너에서 객체 생성을 제어할 수 있습니다.



[^1]: 레시피란 Bean Definition이 객체를 어떻게 생성할지, 어떤 속성을 설정할지, 어떤 의존성을 주입할지를 명시한 설명서 또는 설계도와 같은 역할을 한다는 것을 비유적으로 표현한 것입니다. 

이렇게 정리된 내용은 스프링의 Bean Definition과 그 역할을 보다 명확하게 이해하는 데 도움이 될 것입니다.



