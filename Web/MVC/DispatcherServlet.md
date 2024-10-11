[원본](https://docs.spring.io/spring-framework/reference/web/webmvc.html)을 번역하여 작성하였습니다.

![Dispatcher Servlet](./Resource/DispatcherServlet/Dispatcher%20Servlet.png)


### DispatcherServlet: Spring MVC와 반응형 스택의 차이점

Spring MVC는 많은 웹 프레임워크들과 마찬가지로 **프론트 컨트롤러 패턴**을 기반으로 설계되었습니다. 이 패턴에서는 **DispatcherServlet**이라는 중앙 서블릿이 요청 처리를 위한 공유 알고리즘을 제공하며, 실제 작업은 구성 가능한 위임 컴포넌트(delegate components)에 의해 수행됩니다. 이러한 모델은 유연하게 설계되어 다양한 워크플로우를 지원할 수 있습니다.

DispatcherServlet은 일반적인 서블릿과 마찬가지로 서블릿 명세에 따라 선언되고 매핑되어야 합니다. 이는 Java 설정을 사용하거나 `web.xml` 파일을 통해 설정할 수 있습니다. DispatcherServlet은 Spring 설정을 사용하여 요청 매핑, 뷰 해석(view resolution), 예외 처리 등의 작업을 수행하는 위임 컴포넌트를 탐색하고 설정합니다.


#### Java Configuration 예제
다음 Java 설정 예제는 `WebApplicationInitializer` 인터페이스를 구현하여 DispatcherServlet을 등록하고 초기화하는 코드입니다. 이 코드에서 DispatcherServlet은 서블릿 컨테이너에 의해 자동으로 감지되고, 설정됩니다.

```java
public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) {

        // Spring 웹 애플리케이션 설정 로드
        AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
        context.register(AppConfig.class);

        // DispatcherServlet 생성 및 등록
        DispatcherServlet servlet = new DispatcherServlet(context);
        ServletRegistration.Dynamic registration = servletContext.addServlet("app", servlet);
        registration.setLoadOnStartup(1);
        registration.addMapping("/app/*");
    }
}
```

위 코드에서는 `AnnotationConfigWebApplicationContext`를 사용하여 애플리케이션 설정을 로드하고, 이를 기반으로 DispatcherServlet을 생성합니다. 그 후 `ServletContext`에 서블릿을 등록하고 URL 매핑을 `/app/*`로 지정합니다.

#### XML Configuration 예제 (`web.xml`)
다음 예제는 `web.xml`을 사용하여 DispatcherServlet을 구성하고 초기화하는 코드입니다.

```xml
<web-app>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/app-context.xml</param-value>
    </context-param>

    <servlet>
        <servlet-name>app</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value></param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>app</servlet-name>
        <url-pattern>/app/*</url-pattern>
    </servlet-mapping>
</web-app>
```

위의 설정에서 `ContextLoaderListener`를 사용하여 `WEB-INF/app-context.xml` 경로에 있는 Spring 애플리케이션 컨텍스트를 로드하고, DispatcherServlet을 초기화합니다. `contextConfigLocation` 속성의 값을 비워두면 기본 설정을 사용하게 됩니다.

### Spring Boot에서의 DispatcherServlet 초기화
Spring Boot에서는 기존의 Spring MVC와는 다른 초기화 방식을 따릅니다. Spring Boot는 서블릿 컨테이너의 생명주기(lifecycle)에 직접 연결되지 않고, Spring의 설정을 통해 자체적으로 부트스트랩 및 내장 서블릿 컨테이너를 시작합니다. 즉, `web.xml`이나 명시적인 `DispatcherServlet` 초기화가 필요하지 않습니다. 

Spring Boot의 초기화 과정에서 **필터(Filter)** 및 **서블릿(Servet)** 선언은 Spring 설정에서 자동으로 감지되고, 이를 서블릿 컨테이너에 등록합니다. 이로 인해 개발자는 애플리케이션 초기화 로직을 간단하게 관리할 수 있으며, Spring Boot의 자동 설정(autoconfiguration) 기능을 활용하여 여러 서블릿 컴포넌트들을 쉽게 설정할 수 있습니다.

예를 들어, 다음과 같은 Spring Boot의 애플리케이션 클래스가 있다고 가정해 봅시다.

```java
@SpringBootApplication
public class MySpringBootApplication {
    public static void main(String[] args) {
        SpringApplication.run(MySpringBootApplication.class, args);
    }
}
```

이 코드에서는 `@SpringBootApplication` 어노테이션을 통해 Spring 애플리케이션의 모든 설정이 자동으로 처리됩니다. DispatcherServlet 역시 자동으로 등록되고 `/` 패턴에 매핑됩니다.


### **DispatcherServlet 아키텍처 흐름*
![Spring Mvc](./Resource/DispatcherServlet/Spring%20MVC%20Diagram.png)

Spring MVC 아키텍처는 Model-View-Controller 패턴을 기반으로 하여 웹 애플리케이션에서 요청을 처리하고 응답을 생성하는 구조입니다.

#### 클라이언트 요청 (Client Request)
- 사용자가 브라우저에서 특정 URL로 요청을 보냅니다. 이 요청은 HTTP 프로토콜을 통해 전달됩니다.

#### Dispatcher Servlet
- **역할**: 모든 요청의 중앙 처리 지점입니다.
- 클라이언트의 요청을 받으면, 이를 분석하고 적절한 핸들러(컨트롤러)를 선택합니다.
- Spring 프레임워크에서 설정된 URL 매핑 정보를 기반으로 요청을 처리할 핸들러를 결정합니다.

#### Handler Mapping
- **역할**: 요청 URL에 기반하여 적절한 핸들러(컨트롤러)를 찾아 매핑합니다.
- Dispatcher Servlet은 Handler Mapping을 사용하여 요청에 적합한 컨트롤러를 선택합니다.
- 각 요청 URL에 대해 어떤 컨트롤러가 처리할지 매핑 정보가 저장되어 있습니다.

#### Handler Adapter
- **역할**: 선택된 핸들러를 호출할 수 있도록 지원하는 컴포넌트입니다.
- Handler Mapping에 의해 선택된 핸들러(컨트롤러)를 호출하기 위해 Handler Adapter가 사용됩니다.
- 이 어댑터는 컨트롤러의 메소드를 실행하고, 필요 시 요청 및 응답 객체를 전달합니다.

#### Controller
- **역할**: 비즈니스 로직을 처리하는 중심적인 역할을 수행합니다.
- 요청을 받고, 해당 요청에 대한 비즈니스 로직을 수행합니다. 필요한 경우 서비스 계층을 호출하여 데이터 처리를 합니다.
- 작업이 완료되면, 결과 데이터 모델과 함께 응답할 뷰의 이름을 반환합니다.

#### Service Layer
- **역할**: 비즈니스 로직을 담당합니다.
- Controller로부터 받은 요청을 처리하고, 필요한 데이터에 대한 CRUD 작업을 수행하기 위해 Repository를 호출합니다.
- 필요한 로직을 수행하고 결과를 컨트롤러에 반환합니다.

#### Repository Layer
- **역할**: 데이터베이스와의 상호작용을 처리합니다.
- 데이터베이스에 접근하여 필요한 데이터를 가져오거나 저장하는 역할을 합니다.
- JPA, MyBatis 등과 같은 데이터 접근 기술을 사용하여 데이터베이스와 통신합니다.

#### Model
- **역할**: 컨트롤러가 처리한 결과를 담는 데이터 객체입니다.
- 뷰에 전달할 데이터를 포함합니다. 이 데이터는 사용자가 요청한 정보일 수 있습니다.

#### View Resolver
- **역할**: 컨트롤러가 반환한 뷰 이름을 실제 뷰로 변환합니다.
- 뷰 이름을 기반으로 적절한 뷰 파일의 경로를 결정하고, 그에 따라 뷰를 생성합니다.
- JSP, Thymeleaf, FreeMarker 등 다양한 템플릿 엔진과 함께 사용할 수 있습니다.

#### View
- **역할**: 최종적으로 클라이언트에게 전달될 HTML 등의 결과를 생성합니다.
- View Resolver가 반환한 뷰 파일을 사용하여, 모델 데이터를 포함한 최종 결과를 생성합니다.
- 생성된 HTML은 Dispatcher Servlet을 통해 클라이언트에게 응답으로 전달됩니다.

#### Response
- 최종적으로 클라이언트에게 응답이 전달됩니다. 이 응답은 사용자가 요청한 페이지나 데이터를 포함합니다.

#### 전체 흐름 요약
1. 클라이언트가 HTTP 요청을 보냄.
2. Dispatcher Servlet이 요청을 받고, Handler Mapping을 통해 적절한 핸들러를 찾음.
3. Handler Adapter가 선택된 핸들러(컨트롤러)를 호출.
4. Controller가 비즈니스 로직을 실행하고, 결과 모델과 뷰 이름을 반환.
5. View Resolver가 뷰 이름을 기반으로 뷰를 결정.
6. View가 최종적으로 HTML 응답을 생성.
7. Dispatcher Servlet이 생성된 응답을 클라이언트에게 전달.

---
### **Servlet 컨테이너**와 **Spring MVC 컨테이너**의 아키텍처
![servlet컨테이너와 spring mvc 컨테이너 아키텍처 흐름](./Resource/DispatcherServlet/Servlet과%20Spring%20MVC%20아키텍처%20개요.png)

#### 1. **Servlet Container (서블릿 컨테이너)**
- **Servlet Container**는 웹 서버 내에서 서블릿을 관리하고, HTTP 요청을 받아서 서블릿 클래스의 메서드를 실행합니다.
- 처리 흐름은 다음과 같습니다:
  - `load Servlet`: 서블릿 클래스를 로드합니다.
  - `Initialisation`: 서블릿을 초기화하여 `init()` 메서드를 실행합니다.
  - `service()`: HTTP 요청에 따라 `doGet()` 또는 `doPost()` 메서드를 호출합니다.
  - `destroy()`: 서블릿을 종료할 때 호출되어 리소스를 해제합니다.
- **Servlet Filter**는 사용자의 요청이 서블릿에 도달하기 전에 특정 작업을 수행할 수 있는 필터로, 로깅, 인증, 권한 확인 등의 작업을 처리할 수 있습니다.

#### 2. **Spring MVC Container (Spring MVC 컨테이너)**
- Spring MVC는 Spring 프레임워크의 웹 애플리케이션 개발을 위한 구조입니다. 이 구조에서는 주로 **DispatcherServlet**이 중앙 역할을 담당합니다.
- **DispatcherServlet**은 Servlet 컨테이너에서 동작하며, Spring의 다양한 구성 요소와 협력하여 요청을 처리합니다.
- Spring MVC의 처리 흐름은 다음과 같습니다:
  - **Controller**: 사용자의 요청을 받고 비즈니스 로직을 처리한 후, 모델 데이터를 준비하여 뷰로 반환합니다.
  - **Service**: 컨트롤러와는 달리 비즈니스 로직을 분리하여 관리하는 서비스 레이어입니다. 이 예시에서는 컨트롤러가 직접 호출되지 않는 구조로 나타내고 있습니다.
  
#### 3. **Request/Response 흐름**
- 사용자가 요청(Request)을 보낼 때, 먼저 **Servlet Filter**를 통과한 후 서블릿 컨테이너가 해당 서블릿을 처리합니다.
- 요청이 `DispatcherServlet`에 도달하면 Spring MVC가 이를 받아 `Controller`에서 해당 로직을 수행하고, 필요한 데이터나 뷰를 반환합니다.
- 이후 생성된 응답(Response)은 다시 사용자의 클라이언트로 전달됩니다.

#### 4. **Shared Config: web.xml**
- 이 XML 파일은 서블릿의 설정을 정의하는 곳으로, 서블릿 초기화 파라미터, 필터 및 서블릿 매핑 등을 지정할 수 있습니다.
  
#### 5. **Spring Container와 Spring MVC Container의 차이**
- **Spring Container**는 전체 애플리케이션의 빈을 관리하며, 비즈니스 로직이나 데이터 접근 등의 비 웹 관련 빈을 포함합니다.
- **Spring MVC Container**는 웹 애플리케이션의 MVC 패턴에 초점을 맞추어, 주로 `Controller`와 같은 웹 관련 빈을 관리합니다.

이 이미지는 전통적인 서블릿 기반 웹 애플리케이션이 Spring MVC와 어떻게 연동되는지, 그리고 요청과 응답이 어떻게 처리되는지를 이해하는 데 도움이 되는 개념도입니다.

### Reactive Stack에서의 DispatcherServlet 대안
Spring WebFlux는 Spring MVC와 다른 **반응형(Reactive)** 웹 프레임워크로, `DispatcherHandler`를 사용하여 요청을 처리합니다. WebFlux에서는 Servlet API 대신 **Reactor** 기반의 `Mono`와 `Flux`를 사용하여 비동기 및 논블로킹(non-blocking) 방식으로 요청을 처리합니다. DispatcherHandler는 DispatcherServlet과 유사한 역할을 수행하지만, 반응형 프로그래밍 패러다임에 맞게 최적화되어 있습니다.

#### Spring WebFlux DispatcherHandler 설정 예제
WebFlux를 사용하여 애플리케이션을 구성하려면, `@EnableWebFlux` 어노테이션을 사용하고 `WebFluxConfigurer`를 구현하여 구성합니다. DispatcherHandler는 자동으로 설정되며, WebFlux 애플리케이션 컨텍스트를 기반으로 동작합니다.

```java
@Configuration
@EnableWebFlux
public class WebFluxConfig implements WebFluxConfigurer {
    // WebFlux 설정을 여기에 추가합니다.
}
```

DispatcherServlet은 주로 전통적인 `Servlet` 기반의 동기적 요청 처리를 위한 것이며, 반면 `DispatcherHandler`는 비동기적인 데이터 스트림을 활용하여 논블로킹으로 요청을 처리합니다. 이를 통해 높은 동시성 처리와 낮은 리소스 소비를 가능하게 합니다.

따라서, 기존 Spring MVC 애플리케이션에서 Spring WebFlux로 전환하거나, 두 스택을 동시에 사용하여 **멀티-모드 애플리케이션**을 구성할 수 있습니다. 이때 `DispatcherServlet`과 `DispatcherHandler`가 각각 전통적인 요청과 반응형 요청을 별도로 처리하게 됩니다.

### 결론
Spring MVC와 WebFlux는 각각 동기식 및 비동기식 요청 처리에 최적화된 프레임워크입니다. 전통적인 Spring MVC 애플리케이션에서는 DispatcherServlet이 중심이 되어 위임 컴포넌트를 관리하지만, WebFlux에서는 DispatcherHandler가 같은 역할을 수행합니다. Spring Boot 환경에서는 이러한 설정들이 자동으로 관리되므로 개발자는 더 간편하게 설정 및 초기화를 할 수 있습니다.

이와 같은 설정 방식의 차이를 이해하면, 필요에 따라 애플리케이션의 구조와 요청 처리 방식을 유연하게 선택할 수 있습니다.

### Context Hierarchy (컨텍스트 계층 구조)

`DispatcherServlet`은 자체적인 설정을 위해 `WebApplicationContext`(일반 `ApplicationContext`의 확장)를 필요로 합니다. `WebApplicationContext`는 `ServletContext` 및 연결된 `Servlet`에 대한 링크를 가지고 있으며, `ServletContext`에 바인딩되어 있어 애플리케이션에서 `RequestContextUtils`의 정적 메서드를 사용하여 `WebApplicationContext`에 접근할 수 있습니다.

대부분의 애플리케이션에서는 단일 `WebApplicationContext`를 사용하는 것이 간단하고 충분합니다. 하지만 하나의 루트 `WebApplicationContext`가 여러 `DispatcherServlet`(또는 다른 `Servlet`) 인스턴스에 걸쳐 공유되고, 각각의 `Servlet`이 자신만의 자식 `WebApplicationContext` 구성을 가지는 컨텍스트 계층 구조를 갖는 것도 가능합니다. 컨텍스트 계층 구조 기능에 대한 자세한 내용은 "ApplicationContext의 추가 기능" 섹션을 참고하십시오.

루트 `WebApplicationContext`는 일반적으로 데이터 리포지토리 및 비즈니스 서비스와 같은 여러 `Servlet` 인스턴스 간에 공유되어야 하는 인프라스트럭처 빈을 포함하고 있습니다. 이러한 빈은 각 `Servlet`의 자식 `WebApplicationContext`에서 상속되고, 필요 시 재정의(즉, 다시 선언)할 수 있습니다. 각 `Servlet`별 자식 `WebApplicationContext`에는 주로 해당 `Servlet`에 국한된 로컬 빈이 포함됩니다. 다음 이미지는 이러한 관계를 보여줍니다.

#### 예시: `WebApplicationContext` 계층 구조 설정

다음 예제는 `WebApplicationContext` 계층 구조를 설정하는 방법을 보여줍니다.

**Java 코드:**

```java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

	@Override
	protected Class<?>[] getRootConfigClasses() {
		return new Class<?>[] { RootConfig.class };
	}

	@Override
	protected Class<?>[] getServletConfigClasses() {
		return new Class<?>[] { App1Config.class };
	}

	@Override
	protected String[] getServletMappings() {
		return new String[] { "/app1/*" };
	}
}
```

위 코드에서:

- `getRootConfigClasses()` 메서드는 루트 `WebApplicationContext`의 설정 클래스를 반환합니다.
- `getServletConfigClasses()` 메서드는 각각의 `DispatcherServlet`에 대한 개별 자식 `WebApplicationContext` 설정 클래스를 반환합니다.
- `getServletMappings()` 메서드는 특정 `DispatcherServlet`이 처리할 URL 패턴을 정의합니다.

만약 애플리케이션이 컨텍스트 계층 구조를 필요로 하지 않는 경우, `getRootConfigClasses()`에서 모든 구성을 반환하고 `getServletConfigClasses()`에서 `null`을 반환하여 간단하게 설정할 수 있습니다.

### `web.xml`을 사용한 예제

다음은 위의 자바 설정과 동등한 `web.xml` 설정 예시입니다:

```xml
<web-app>

	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>

	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/root-context.xml</param-value>
	</context-param>

	<servlet>
		<servlet-name>app1</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>/WEB-INF/app1-context.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>

	<servlet-mapping>
		<servlet-name>app1</servlet-name>
		<url-pattern>/app1/*</url-pattern>
	</servlet-mapping>

</web-app>
```

위 설정에서는:

1. `ContextLoaderListener`가 루트 `WebApplicationContext`를 설정하기 위해 사용됩니다.
2. `context-param`으로 `contextConfigLocation`을 지정하여 루트 컨텍스트 설정 파일의 위치(`root-context.xml`)를 정의합니다.
3. `DispatcherServlet`(`app1`)이 정의되고, `init-param`을 통해 해당 `DispatcherServlet`의 전용 `WebApplicationContext` 설정 파일(`app1-context.xml`) 위치를 지정합니다.
4. `Servlet`이 `/app1/*` URL 패턴을 처리하도록 매핑됩니다.

만약 애플리케이션이 컨텍스트 계층 구조를 필요로 하지 않는다면, 루트 컨텍스트만 설정하고 `Servlet`의 `contextConfigLocation` 파라미터를 비워 둘 수 있습니다.

### Context Hierarchy의 의미와 활용

`WebApplicationContext`의 계층 구조는 큰 애플리케이션에서 모듈화된 구성을 사용하여 특정 `Servlet`이 공유해야 하는 공통 빈(예: 데이터베이스 설정, 보안 서비스 등)과 각 `Servlet`별로 필요한 개별 빈(예: 특정 컨트롤러, 서비스 로직 등)을 명확히 분리할 수 있도록 돕습니다. 이러한 계층 구조를 사용하면 다음과 같은 장점이 있습니다:

1. **공유 설정의 재사용**: 데이터베이스와 같은 공통 인프라 빈을 루트 컨텍스트에 배치하여 여러 `DispatcherServlet`에서 이를 재사용할 수 있습니다.
2. **모듈별 설정 분리**: 각 `DispatcherServlet`의 자식 컨텍스트에 특정 모듈의 설정을 정의하여 모듈 간 의존성을 줄이고, 유지보수를 쉽게 할 수 있습니다.
3. **의존성 관리**: 특정 모듈이 상위 모듈의 빈을 참조할 수 있지만, 상위 모듈은 하위 모듈의 빈을 참조하지 않도록 하여 의존성 흐름을 명확하게 할 수 있습니다.

### Context 계층 구조가 필요한 경우

대부분의 간단한 애플리케이션에서는 단일 `WebApplicationContext`만 사용해도 무방하지만, 다음과 같은 경우에는 컨텍스트 계층 구조가 유용합니다:

- **여러 모듈로 구성된 대규모 애플리케이션**: 서로 다른 팀이 각기 다른 모듈을 개발하고, 이러한 모듈이 하나의 애플리케이션에서 동작해야 하는 경우.
- **공통 인프라 서비스 분리**: 보안, 데이터베이스 연결 등과 같은 공통 인프라 서비스는 전체 애플리케이션에 걸쳐 공유되지만, 각 `DispatcherServlet`은 각기 다른 비즈니스 로직을 처리해야 할 때.

### 요약

`WebApplicationContext`의 계층 구조는 스프링 기반의 웹 애플리케이션에서 모듈화된 구성과 공통 설정의 재사용성을 가능하게 하여, 복잡한 애플리케이션 구조를 효율적으로 관리할 수 있도록 합니다. 필요에 따라 루트와 자식 `WebApplicationContext`를 분리하고, 설정 파일이나 자바 클래스를 통해 각 계층을 정의할 수 있습니다.



### Spring Special Beans Overview
DispatcherServlet은 여러 특별한 빈들을 사용하여 요청을 처리하고 적절한 응답을 렌더링합니다. 이러한 빈은 스프링이 관리하는 객체로, 각 빈은 특정 계약을 준수하며, 기본적으로 프레임워크의 표준 구현을 제공합니다. 그러나 필요에 따라 속성을 맞춤 설정하거나 확장 및 대체할 수 있습니다.  

| **Bean Type**                   | **Description**                                                                                                               |
|---------------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| **HandlerMapping**              | 요청을 핸들러와 매핑하고, 사전/사후 처리할 인터셉터 목록을 관리합니다.                                                        |
| **HandlerAdapter**              | 다양한 핸들러의 호출 방식을 `DispatcherServlet`에서 추상화하여 일관성 있는 호출을 지원합니다.                                 |
| **HandlerExceptionResolver**    | 예외 발생 시 특정 핸들러, 에러 뷰, 또는 다른 타겟에 매핑하여 예외를 처리합니다.                                               |
| **ViewResolver**                | 핸들러가 반환한 논리적 뷰 이름을 실제 `View` 객체로 변환하여 응답을 렌더링합니다.                                              |
| **LocaleResolver**              | 클라이언트의 지역 정보를 확인하여 국제화된 뷰를 제공합니다.                                                                   |
| **ThemeResolver**               | 애플리케이션의 사용자 맞춤 테마를 관리하고 제공합니다.                                                                          |
| **MultipartResolver**           | 파일 업로드와 같은 멀티파트 요청을 처리하기 위해 멀티파트 파싱을 지원합니다.                                                    |
| **FlashMapManager**             | 리다이렉트 간 플래시 속성을 저장하고 다음 요청에 전달합니다.                                                                    |

자세한 내용은 [Spring Documentation](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/special-bean-types.html)을 참고하세요.


### Web MVC Configuration

Spring 애플리케이션은 `DispatcherServlet`에서 요청을 처리하기 위해 필요한 여러 인프라 빈(`HandlerMapping`, `ViewResolver` 등)을 선언할 수 있습니다. `DispatcherServlet`은 `WebApplicationContext`에서 이러한 특별한 빈을 확인하며, 해당 빈이 없으면 `DispatcherServlet.properties`의 기본 타입을 사용합니다.

대부분의 경우, `MVC Config`를 시작점으로 사용하는 것이 좋습니다. 이 설정은 필수 빈을 자바 또는 XML로 선언하고, API 콜백을 통해 손쉽게 사용자 정의할 수 있습니다.

자세한 내용은 [Spring Documentation](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/special-bean-types.html)을 참고하세요.

### Web MVC Configuration

Spring 애플리케이션은 `DispatcherServlet`에서 요청을 처리하기 위해 필요한 여러 인프라 빈(`HandlerMapping`, `ViewResolver` 등)을 선언할 수 있습니다. `DispatcherServlet`은 `WebApplicationContext`에서 이러한 특별한 빈을 확인하며, 해당 빈이 없으면 `DispatcherServlet.properties`의 기본 타입을 사용합니다.

대부분의 경우, `MVC Config`를 시작점으로 사용하는 것이 좋습니다. 이 설정은 필수 빈을 자바 또는 XML로 선언하고, API 콜백을 통해 손쉽게 사용자 정의할 수 있습니다.

자세한 내용은 [Spring Documentation](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/special-bean-types.html)을 참고하세요.

### Servlet 환경 설정 요약

`DispatcherServlet`을 Servlet 환경에서 설정할 때는 `WebApplicationInitializer`를 구현하여 프로그래밍 방식으로 Servlet을 등록할 수 있습니다. 자바 기반 설정을 사용할 때는 `AbstractAnnotationConfigDispatcherServletInitializer`를 확장하고, XML 기반 설정 시에는 `AbstractDispatcherServletInitializer`를 사용합니다. 또한, `AbstractDispatcherServletInitializer`는 필터를 자동으로 `DispatcherServlet`에 매핑하고 비동기 지원을 활성화할 수 있는 편리한 메서드도 제공합니다.

자세한 내용은 [Spring Documentation](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/special-bean-types.html)을 참고하세요.

## Processing

### DispatcherServlet 요청 처리 과정

DispatcherServlet은 다음과 같은 단계로 요청을 처리합니다:

1.  **WebApplicationContext 검색 및 바인딩**
    -   요청 처리 과정에서 WebApplicationContext가 검색되어 요청의 속성으로 바인딩됩니다. 이 컨텍스트는 컨트롤러 및 다른 구성 요소들이 사용할 수 있습니다. 기본적으로 DispatcherServlet.WEB\_APPLICATION\_CONTEXT\_ATTRIBUTE라는 키로 바인딩됩니다.
2.  **locale resolver 바인딩**
    -   요청에 로케일 리졸버가 바인딩되어, 요청 처리 시 어떤 로케일을 사용할지 결정합니다(예: 뷰 렌더링, 데이터 준비 등). 로케일 처리가 필요하지 않은 경우, 로케일 리졸버는 필요하지 않습니다.
3.  **theme resolver 바인딩**
    -   요청에 테마 리졸버가 바인딩되어, 뷰와 같은 요소들이 사용할 테마를 결정합니다. 테마를 사용하지 않는 경우, 이를 무시할 수 있습니다.
4.  **multipart file resolver 검사**
    -   멀티파트 파일 리졸버를 지정한 경우, 요청이 멀티파트인지 검사합니다. 멀티파트가 발견되면, 요청은 MultipartHttpServletRequest로 래핑되어 다른 요소들이 추가 처리를 할 수 있도록 합니다. 멀티파트 처리에 대한 자세한 내용은 Multipart Resolver를 참조하십시오.
5.  **Handler 검색**
    -   적절한 핸들러를 검색합니다. 핸들러가 발견되면, 핸들러와 관련된 실행 체인(전처리기, 후처리기, 컨트롤러)이 실행되어 렌더링할 모델을 준비합니다. 또는, 주석이 달린 컨트롤러의 경우, 뷰를 반환하는 대신 HandlerAdapter 내에서 응답을 렌더링할 수 있습니다.
6.  **Model 반환 및 View Rendering**
    -   모델이 반환되면 뷰가 렌더링됩니다. 반대로, 모델이 반환되지 않는 경우(전처리기나 후처리기에 의해 요청이 차단되었을 수 있음), 뷰는 렌더링되지 않습니다. 이는 요청이 이미 처리되었을 수 있음을 의미합니다.
7.  **예외 해결**
    -   요청 처리 중에 발생한 예외는 WebApplicationContext에 선언된 HandlerExceptionResolver 빈을 사용하여 해결합니다. 이러한 예외 리졸버는 예외를 처리하기 위한 사용자 정의 로직을 허용합니다. 자세한 내용은 Exceptions를 참조하십시오.
8.  **HTTP 캐싱 지원**
    -   핸들러는 WebRequest의 checkNotModified 메서드를 사용하여 HTTP 캐싱을 지원할 수 있으며, 주석이 달린 컨트롤러에 대한 추가 옵션이 제공됩니다. 이와 관련된 내용은 HTTP Caching for Controllers를 참조하십시오.

### DispatcherServlet 초기화 파라미터

DispatcherServlet 인스턴스를 사용자 정의하려면, web.xml 파일의 서블릿 선언에 Servlet 초기화 매개변수(init-param)를 추가할 수 있습니다. 다음 표는 지원되는 매개변수를 설명합니다.

매개변수설명

| **contextClass** | ConfigurableWebApplicationContext를 구현하는 클래스. 기본적으로 XmlWebApplicationContext가 사용됩니다. |
| --- | --- |
| **contextConfigLocation** | 컨텍스트 인스턴스에 전달되는 문자열로, 컨텍스트가 위치하는 곳을 나타냅니다. 여러 문자열을 사용하여 여러 컨텍스트를 지원할 수 있으며, 동일한 빈이 정의된 경우 최신 위치가 우선합니다. |
| **namespace** | WebApplicationContext의 네임스페이스입니다. 기본값은 \[servlet-name\]-servlet입니다. |
| **throwExceptionIfNoHandlerFound** | 요청에 대한 핸들러가 발견되지 않았을 때 NoHandlerFoundException을 발생시킬지 여부를 결정합니다. 이 예외는 HandlerExceptionResolver로 처리할 수 있습니다. (예: @ExceptionHandler 메서드 사용) 현재 6.1 버전에서 기본값은 true이며, 더 이상 사용되지 않습니다. |
| **기타 주의사항** | 기본 서블릿 처리가 구성된 경우, 해결되지 않은 요청은 항상 기본 서블릿으로 전달되며, 404 오류는 발생하지 않습니다. |

이렇게 DispatcherServlet은 요청을 처리하고, 요청 처리 과정에서 발생하는 다양한 요소들을 바인딩하고 관리합니다.

## Path Matching

Servlet API는 전체 요청 경로를 requestURI로 노출하며, 이를 contextPath, servletPath, pathInfo로 세분화합니다. 이 값들은 서블릿이 어떻게 매핑되었는지에 따라 달라집니다. Spring MVC는 이러한 입력을 바탕으로 핸들러 매핑에 사용할 조회 경로(lookup path)를 결정해야 하며, 이 때 contextPath와 서블릿 매핑 접두사를 제외해야 합니다.

#### 경로 처리의 문제점

1.  **서블릿 경로와 경로 정보의 디코딩**
    -   servletPath와 pathInfo는 디코딩되어 있어, 전체 requestURI와 직접 비교하여 조회 경로를 유도하기가 어렵습니다. 그래서 requestURI를 디코딩해야 합니다. 하지만 이는 보안 문제를 유발할 수 있는 인코딩된 예약 문자가 포함된 경로 구조를 변경할 수 있습니다.
    -   예를 들어, 예약 문자인 "/" 또는 ";"가 디코딩될 경우, 경로 구조가 변경될 수 있습니다. 게다가, 서블릿 컨테이너는 servletPath를 다양한 정도로 정규화하므로 requestURI와의 startsWith 비교가 어려워집니다.
2.  **서블릿 경로 사용의 불리함**
    -   이러한 이유로, 서블릿 경로에 대한 의존도를 피하는 것이 좋습니다. 만약 DispatcherServlet이 기본 서블릿으로 "/" 또는 "/\*"로 매핑되어 있고 서블릿 컨테이너가 4.0 이상인 경우, Spring MVC는 서블릿 매핑 유형을 감지하고 servletPath와 pathInfo의 사용을 피할 수 있습니다.
    -   3.1 서블릿 컨테이너의 경우, 동일한 서블릿 매핑 유형을 사용하면, MVC 구성에서 alwaysUseFullPath=true를 통해 UrlPathHelper를 제공하여 동일한 효과를 얻을 수 있습니다.
3.  **요청 URI 디코딩의 문제점**
    -   기본 서블릿 매핑 "/"는 좋은 선택이지만, 여전히 요청 URI를 디코딩해야 컨트롤러 매핑과 비교할 수 있습니다. 그러나 이는 예약 문자를 디코딩함으로써 경로 구조를 변경할 수 있다는 위험이 있습니다. 이러한 문자가 예상되지 않는 경우(예: Spring Security HTTP 방화벽처럼) 이를 거부하거나, UrlPathHelper를 urlDecode=false로 구성할 수 있습니다. 그러나 이 경우 컨트롤러 매핑은 인코딩된 경로와 일치해야 하므로 잘 작동하지 않을 수 있습니다.
    -   게다가, DispatcherServlet이 다른 서블릿과 URL 공간을 공유해야 하거나 매핑이 필요할 경우 접두사로 매핑해야 할 수 있습니다.

### PathPatternParser의 도입

이러한 문제는 Spring MVC의 5.3 버전부터 사용 가능한 PathPatternParser와 파싱된 패턴을 사용함으로써 해결됩니다. 이는 기본적으로 6.0 버전에서 활성화됩니다.

-   **PathPatternParser의 장점**
    -   AntPathMatcher와 달리, PathPatternParser는 조회 경로를 디코딩하거나 컨트롤러 매핑을 인코딩할 필요 없이 패턴을 매칭할 수 있습니다. 이는 요청 경로(RequestPath)라고 불리는 경로의 파싱된 표현에 기반하여 경로의 각 세그먼트를 개별적으로 매칭합니다.
    -   이를 통해 경로 세그먼트 값을 개별적으로 디코딩하고 정리할 수 있으며, 경로 구조를 변경할 위험 없이 처리할 수 있습니다.
    -   또한, 파싱된 패턴은 서블릿 경로 매핑을 접두사로 사용하는 것을 지원하며, 이 때 접두사는 간단해야 하며 인코딩된 문자가 없어야 합니다.

이렇게 \`PathPatternParser\`와 파싱된 패턴을 사용함으로써 경로 일치의 문제를 효과적으로 해결할 수 있습니다. 이는 Spring MVC의 경로 처리 방식에 유연성과 안정성을 추가합니다.

## Interception

인터셉션은 모든 HandlerMapping 구현에서 지원되며, 요청 간에 기능을 적용할 때 유용합니다. **HandlerInterceptor**를 사용하여 특정 요청 처리 전후에 코드를 실행할 수 있습니다. 주요 메서드는 다음과 같습니다:

1.  **preHandle(..)**
    -   이 메서드는 실제 핸들러가 실행되기 전에 호출됩니다.
    -   반환값이 **true**이면, 요청 처리 체인이 계속 진행되고, 핸들러가 호출됩니다.
    -   반환값이 **false**이면, 나머지 실행 체인이 건너뛰어지며 핸들러가 호출되지 않습니다.
    -   주로 요청 인증, 세션 검사 등의 기능을 구현하는 데 사용됩니다.
2.  **postHandle(..)**
    -   이 메서드는 핸들러가 실행된 후에 호출됩니다.
    -   이 시점에서 모델 데이터나 뷰 정보를 조작할 수 있습니다.
    -   주로 로깅이나 추가 데이터 추가 등을 위한 용도로 사용됩니다.
3.  **afterCompletion(..)**
    -   이 메서드는 요청 처리가 완전히 종료된 후에 호출됩니다.
    -   주로 리소스 정리, 로그 기록 등을 위한 용도로 사용됩니다.

### 주의사항

-   **@ResponseBody 및 ResponseEntity 사용 시**:
    -   @ResponseBody 또는 ResponseEntity를 사용하는 컨트롤러 메서드의 경우, 응답은 HandlerAdapter 내에서 작성되고 커밋됩니다. 이 때문에 postHandle이 호출되기 전에 이미 응답이 완료되어, 추가 헤더를 추가하는 등의 변경은 불가능합니다.
    -   이러한 경우에는 **ResponseBodyAdvice**를 구현하고 이를 Controller Advice 빈으로 선언하거나 RequestMappingHandlerAdapter에 직접 구성하여 사용해야 합니다.

### 인터셉터 구성

-   인터셉터를 구성하는 방법에 대한 예시는 MVC 구성의 **Interceptors** 섹션을 참고하면 됩니다.
-   또한, 개별 HandlerMapping 구현에서 setter를 사용하여 인터셉터를 직접 등록할 수도 있습니다.

### 경고

-   인터셉터는 보안 계층으로 사용하는 것이 이상적이지 않습니다. 왜냐하면 주석 기반 컨트롤러의 경로 매칭과의 불일치가 발생할 수 있기 때문입니다.
-   일반적으로 **Spring Security**를 사용하는 것을 추천하며, 대안으로 Servlet 필터 체인과 통합된 유사한 접근 방식을 조기에 적용하는 것이 좋습니다.

인터셉터는 요청의 전처리, 후처리 및 요청 완료 후 처리를 가능하게 하여, 코드 재사용성과 요청 흐름의 제어를 쉽게 해줍니다. 하지만 보안 기능을 구현할 때는 다른 메커니즘을 사용하는 것이 바람직합니다.

## Exceptions

Spring MVC에서 요청 매핑 중에 예외가 발생하거나 요청 핸들러(@Controller)에서 예외가 던져지면, DispatcherServlet은 HandlerExceptionResolver 빈 체인에 위임하여 예외를 처리합니다. 이 과정에서 대개는 오류 응답을 제공합니다.

### HandlerExceptionResolver 구현 목록

다음 표는 사용 가능한 HandlerExceptionResolver 구현 목록입니다:

HandlerExceptionResolver설명

| **SimpleMappingExceptionResolver** | 예외 클래스 이름과 오류 뷰 이름 간의 매핑을 제공합니다. 브라우저 애플리케이션에서 오류 페이지를 렌더링하는 데 유용합니다. |
| --- | --- |
| **DefaultHandlerExceptionResolver** | Spring MVC에서 발생한 예외를 해결하고 이를 HTTP 상태 코드에 매핑합니다. Alternative ResponseEntityExceptionHandler 및 오류 응답을 참조하십시오. |
| **ResponseStatusExceptionResolver** | @ResponseStatus 주석이 있는 예외를 해결하고, 주석의 값에 따라 HTTP 상태 코드에 매핑합니다. |
| **ExceptionHandlerExceptionResolver** | @Controller 또는 @ControllerAdvice 클래스 내의 @ExceptionHandler 메서드를 호출하여 예외를 해결합니다. |

#### 예외 해결 체인 (Chain of Resolvers)

여러 HandlerExceptionResolver 빈을 Spring 구성에 선언하고 순서 속성을 설정하여 예외 해결 체인을 형성할 수 있습니다. 순서 속성이 높을수록 해당 예외 해결자가 늦게 위치하게 됩니다.

HandlerExceptionResolver 계약에 따르면, 다음을 반환할 수 있습니다:

-   오류 뷰를 가리키는 **ModelAndView**.
-   해결자가 예외를 처리한 경우 **빈 ModelAndView**.
-   예외가 해결되지 않은 경우 **null**을 반환하여 다음 해결자가 시도할 수 있도록 합니다. 최종적으로 예외가 해결되지 않으면 Servlet 컨테이너로 다시 전파됩니다.

MVC 구성은 기본 Spring MVC 예외, @ResponseStatus 주석이 있는 예외, @ExceptionHandler 메서드에 대한 지원을 자동으로 선언합니다. 이 목록을 사용자 정의하거나 대체할 수 있습니다.

### 컨테이너 오류 페이지 (Container Error Page)

어떤 HandlerExceptionResolver에 의해서도 예외가 해결되지 않으면, 예외가 전파되거나 응답 상태가 오류 상태(예: 4xx, 5xx)로 설정됩니다. 이 경우, Servlet 컨테이너는 HTML 기본 오류 페이지를 렌더링할 수 있습니다. 기본 오류 페이지를 사용자 정의하려면 web.xml에 오류 페이지 매핑을 선언해야 합니다. 다음 예시는 이를 보여줍니다:

```
<error-page>
    <location>/error</location>
</error-page>
```

위 예제에 따라, 예외가 전파되거나 응답에 오류 상태가 설정되면 Servlet 컨테이너는 구성된 URL(예: /error)로 ERROR 디스패치를 수행합니다. 이후 DispatcherServlet에 의해 처리되며, 해당 URL이 @Controller에 매핑될 수 있습니다. 이 컨트롤러는 오류 뷰 이름과 모델을 반환하거나 JSON 응답을 렌더링할 수 있습니다. 다음 예시는 이를 보여줍니다:

```
@RestController
public class ErrorController {

    @RequestMapping(path = "/error")
    public Map<String, Object> handle(HttpServletRequest request) {
        Map<String, Object> map = new HashMap<>();
        map.put("status", request.getAttribute("jakarta.servlet.error.status_code"));
        map.put("reason", request.getAttribute("jakarta.servlet.error.message"));
        return map;
    }
}
```

### 팁

Servlet API에서는 Java에서 오류 페이지 매핑을 생성하는 방법을 제공하지 않습니다. 그러나 WebApplicationInitializer와 최소한의 web.xml을 함께 사용할 수 있습니다.

Spring MVC에서 예외가 발생했을 때, 다양한 \\\`HandlerExceptionResolver\\\`를 통해 예외를 처리하며, 이를 통해 사용자에게 적절한 오류 응답을 제공합니다. 또한, 컨테이너에서 기본 오류 페이지를 사용자 정의하여 발생한 오류에 대한 정보를 사용자에게 제공할 수 있습니다.

## View Resolution

Spring MVC는 **ViewResolver** 및 **View** 인터페이스를 정의하여 특정 뷰 기술에 구애받지 않고 모델을 브라우저에 렌더링할 수 있도록 합니다. **ViewResolver**는 뷰 이름과 실제 뷰 간의 매핑을 제공합니다. **View**는 특정 뷰 기술에 전달하기 전에 데이터를 준비하는 역할을 합니다.

### ViewResolver 구현 목록

다음 표는 ViewResolver의 구현 목록입니다:

ViewResolver설명

| **AbstractCachingViewResolver** | 이 클래스의 서브클래스는 해결한 뷰 인스턴스를 캐시하여 성능을 개선합니다. cache 속성을 false로 설정하여 캐시를 끌 수 있습니다. 특정 뷰를 런타임에 새로 고쳐야 할 경우, removeFromCache(String viewName, Locale loc) 메서드를 사용할 수 있습니다. |
| --- | --- |
| **UrlBasedViewResolver** | ViewResolver 인터페이스의 간단한 구현으로, 논리 뷰 이름을 URL에 직접 매핑합니다. 명시적인 매핑 정의가 필요하지 않습니다. |
| **InternalResourceViewResolver** | UrlBasedViewResolver의 편리한 서브클래스로, InternalResourceView(즉, Servlets 및 JSP)와 JstlView 등의 서브클래스를 지원합니다. 모든 뷰에 대해 뷰 클래스를 설정하려면 setViewClass(..)를 사용할 수 있습니다. |
| **FreeMarkerViewResolver** | FreeMarkerView 및 사용자 정의 서브클래스를 지원하는 UrlBasedViewResolver의 편리한 서브클래스입니다. |
| **ContentNegotiatingViewResolver** | 요청 파일 이름 또는 Accept 헤더에 따라 뷰를 해결하는 ViewResolver 인터페이스의 구현입니다. Content Negotiation을 참조하십시오. |
| **BeanNameViewResolver** | 현재 애플리케이션 컨텍스트에서 뷰 이름을 bean 이름으로 해석하는 ViewResolver 구현입니다. 매우 유연한 변형으로, 서로 다른 뷰 유형을 믹스앤매치할 수 있습니다. 각 뷰는 XML 또는 구성 클래스에서 bean으로 정의될 수 있습니다. |

### 뷰 해상도 처리 (Handling)

여러 개의 ViewResolver 빈을 선언하고 필요에 따라 순서 속성을 설정하여 뷰 해상도를 체인할 수 있습니다. 순서 속성이 높을수록 뷰 리졸버는 체인에서 나중에 위치하게 됩니다.

ViewResolver 계약에 따르면, 뷰를 찾을 수 없는 경우 null을 반환할 수 있습니다. 그러나 JSP 및 InternalResourceViewResolver의 경우, JSP가 존재하는지 확인하려면 RequestDispatcher를 통해 디스패치해야 합니다. 따라서 InternalResourceViewResolver는 전체 뷰 리졸버 순서에서 마지막에 배치해야 합니다.

뷰 해상도를 구성하는 것은 Spring 구성에 ViewResolver 빈을 추가하는 것만큼 간단합니다. MVC Config는 View Resolvers에 대한 전용 구성 API를 제공하며, 컨트롤러 로직 없이 HTML 템플릿 렌더링에 유용한 로직 없는 View Controllers를 추가하는 데 도움이 됩니다.

### 리다이렉트 (Redirecting)

특별한 **redirect:** 접두사를 뷰 이름에 추가하면 리다이렉트를 수행할 수 있습니다. UrlBasedViewResolver 및 그 하위 클래스는 이를 리다이렉트가 필요하다는 지시로 인식합니다. 뷰 이름의 나머지는 리다이렉트 URL입니다.

이는 컨트롤러가 RedirectView를 반환한 것과 동일한 효과를 내며, 이제 컨트롤러는 논리적 뷰 이름으로 작업할 수 있습니다. 예를 들어, redirect:/myapp/some/resource는 현재 Servlet 컨텍스트에 대해 상대적으로 리다이렉트하고, redirect:[https://myhost.com/some/arbitrary/path](https://myhost.com/some/arbitrary/path)는 절대 URL로 리다이렉트합니다.

### 포워딩 (Forwarding)

UrlBasedViewResolver 및 그 하위 클래스에서 궁극적으로 해결된 뷰 이름에 대해 특별한 **forward:** 접두사를 사용할 수도 있습니다. 이는 InternalResourceView를 생성하며, RequestDispatcher.forward()를 수행합니다. 따라서 이 접두사는 InternalResourceViewResolver 및 InternalResourceView(JSP)에서는 유용하지 않지만, 다른 뷰 기술을 사용할 때도 Servlet/JSP 엔진에 의해 리소스를 강제로 포워드하도록 하는 데 유용합니다. 여러 개의 뷰 리졸버를 체인할 수도 있습니다.

### 콘텐츠 협상 (Content Negotiation)

ContentNegotiatingViewResolver는 뷰를 직접 해결하지 않고, 다른 뷰 리졸버에 위임하여 클라이언트가 요청한 표현과 유사한 뷰를 선택합니다. 표현은 Accept 헤더 또는 쿼리 매개변수(예: "/path?format=pdf")에서 결정할 수 있습니다.

ContentNegotiatingViewResolver는 요청 미디어 유형을 각 뷰 리졸버와 관련된 뷰가 지원하는 미디어 유형(Content-Type)과 비교하여 적절한 뷰를 선택합니다. 호환 가능한 Content-Type을 가진 첫 번째 뷰가 클라이언트에게 표현을 반환합니다. 만약 뷰 리졸버 체인에서 호환 가능한 뷰를 제공할 수 없다면, DefaultViews 속성을 통해 지정된 뷰 목록을 참조합니다. 이 옵션은 논리적 뷰 이름에 관계없이 현재 리소스의 적절한 표현을 렌더링할 수 있는 싱글턴 뷰에 적합합니다. Accept 헤더는 와일드카드(예: text/\*)를 포함할 수 있으며, 이 경우 Content-Type이 text/xml인 뷰가 호환 가능한 매치가 됩니다.

Spring MVC는 다양한 ViewResolver 구현을 통해 뷰 이름을 실제 뷰로 매핑할 수 있도록 하며, 이를 통해 사용자는 다양한 뷰 기술을 선택하여 모델을 브라우저에 렌더링할 수 있습니다. 리다이렉트 및 포워딩을 통해 요청의 흐름을 제어할 수 있으며, 콘텐츠 협상을 통해 클라이언트의 요청에 맞는 적절한 응답을 제공합니다.

## Locale

Spring 프레임워크는 다국어를 지원하는 다양한 기능을 제공합니다. 특히, **Spring Web MVC**는 클라이언트의 로케일에 따라 메시지를 자동으로 해석할 수 있는 기능을 갖추고 있습니다. 이를 통해 **DispatcherServlet**은 요청이 들어올 때 로케일 리졸버를 찾아 로케일을 설정합니다. 로케일은 언어와 지역 정보를 포함하여, 다국어 지원과 관련된 기능을 가능하게 합니다.

### LocaleResolver

로케일 리졸버는 요청을 처리하는 데 사용되며, 다음과 같은 객체들이 포함되어 있습니다:

1.  **Header Resolver**: 클라이언트가 보낸 요청의 Accept-Language 헤더를 검사하여 로케일을 결정합니다. 이 헤더는 일반적으로 클라이언트 운영 체제의 로케일을 포함합니다. 하지만, 이 리졸버는 시간대 정보를 지원하지 않습니다.
2.  **Cookie Resolver**: 클라이언트의 쿠키를 검사하여 Locale이나 TimeZone이 지정되어 있는지 확인합니다. 지정된 경우, 해당 정보를 사용하여 로케일을 설정합니다. 쿠키의 이름과 최대 유효 기간을 설정할 수 있습니다.
3.  <bean id="localeResolver" class="org.springframework.web.servlet.i18n.CookieLocaleResolver"> <property name="cookieName" value="clientlanguage"/> <property name="cookieMaxAge" value="100000"/> </bean>
4.  **Session Resolver**: 세션에 저장된 Locale과 TimeZone을 사용할 수 있는 SessionLocaleResolver를 제공합니다. 이 방법은 사용자의 요청에 대한 세션 내에서 로케일 설정을 저장합니다. 세션이 종료되면 이 설정은 사라집니다.
5.  **Locale Interceptor**: LocaleChangeInterceptor를 추가하여 요청 파라미터에 따라 로케일을 변경할 수 있습니다. 예를 들어, siteLanguage라는 파라미터가 요청에 포함되면, 이 값을 통해 로케일을 변경할 수 있습니다.
6.  <bean id="localeChangeInterceptor" class="org.springframework.web.servlet.i18n.LocaleChangeInterceptor"> <property name="paramName" value="siteLanguage"/> </bean> <bean id="localeResolver" class="org.springframework.web.servlet.i18n.CookieLocaleResolver"/> <bean id="urlMapping" class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping"> <property name="interceptors"> <list> <ref bean="localeChangeInterceptor"/> </list> </property> <property name="mappings"> <value>/\*\*/\*.view=someController</value> </property> </bean>

### Time Zone (시간대)

클라이언트의 로케일 외에도 시간대를 아는 것이 유용할 수 있습니다. **LocaleContextResolver** 인터페이스는 시간대 정보를 포함할 수 있는 더 풍부한 LocaleContext를 제공하는 로케일 리졸버를 제공합니다. 사용자의 시간대는 RequestContext.getTimeZone() 메서드를 사용하여 가져올 수 있습니다. 이 정보는 Spring의 ConversionService에 등록된 모든 날짜/시간 변환기 및 형식 지정기에서 자동으로 사용됩니다.

### 정리

-   **locale resolver**는 클라이언트의 요청에 따라 적절한 로케일을 설정하는 역할을 합니다.
-   **locale interceptor**를 사용하면 요청 파라미터에 따라 로케일을 변경할 수 있습니다.
-   **tiem zone**는 클라이언트의 로케일 외에 유용하게 사용되며, 날짜와 시간 형식 지정에 자동으로 적용됩니다.

이러한 기능들을 통해 Spring MVC는 다국어 웹 애플리케이션을 효과적으로 지원할 수 있습니다.

## Themes

Spring Web MVC 프레임워크에서는 웹 애플리케이션의 전체적인 디자인과 느낌을 설정하기 위해 테마 기능을 제공합니다. 테마는 일반적으로 스타일 시트(CSS)와 이미지로 구성된 정적 자원의 집합으로, 애플리케이션의 시각적 스타일을 변경합니다. 하지만, 주의할 점은 Spring 6.0부터 테마 지원이 더 이상 권장되지 않으며, CSS 사용으로 대체되었습니다.

## Multipart Resolver

**MultipartResolver**는 Spring 프레임워크의 org.springframework.web.multipart 패키지에서 제공하는 인터페이스로, 파일 업로드를 포함한 멀티파트 요청을 처리하기 위한 전략입니다. Spring 6.0부터는 Apache Commons FileUpload에 기반한 구식 **CommonsMultipartResolver**는 더 이상 사용되지 않으며, 대신 **StandardServletMultipartResolver**가 제공됩니다.

### 멀티파트 처리 활성화

멀티파트 파일 업로드를 처리하기 위해서는 DispatcherServlet의 Spring 구성에 **multipartResolver**라는 이름의 MultipartResolver 빈을 선언해야 합니다. DispatcherServlet은 이를 감지하여 들어오는 요청에 적용합니다.

1.  **POST 요청 처리**: Content-Type이 multipart/form-data인 POST 요청이 수신되면, 멀티파트 리졸버는 요청 내용을 분석하여 HttpServletRequest를 MultipartHttpServletRequest로 래핑합니다. 이를 통해 파일과 요청 파라미터에 접근할 수 있습니다.

### Servlet 멀티파트 파싱

Servlet 멀티파트 파싱을 활성화하려면, Servlet 컨테이너의 설정이 필요합니다. 두 가지 방법으로 설정할 수 있습니다:

1.  **Java 코드에서 설정**:
    
    -   MultipartConfigElement를 Servlet 등록에 설정합니다.
    
    ```
    public class AppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
    
        // ...
    
        @Override
        protected void customizeRegistration(ServletRegistration.Dynamic registration) {
            // maxFileSize, maxRequestSize, fileSizeThreshold를 선택적으로 설정할 수 있습니다.
            registration.setMultipartConfig(new MultipartConfigElement("/tmp"));
        }
    }
    ```
    
2.  **web.xml에서 설정**:
    
    -   Servlet 선언에 <multipart-config> 섹션을 추가합니다.
    
    ```
    <servlet>
        <servlet-name>yourServlet</servlet-name>
        <servlet-class>your.package.YourServlet</servlet-class>
        <multipart-config>
            <location>/tmp</location>
            <max-file-size>10485760</max-file-size> <!-- 10 MB -->
            <max-request-size>52428800</max-request-size> <!-- 50 MB -->
            <file-size-threshold>5242880</file-size-threshold> <!-- 5 MB -->
        </multipart-config>
    </servlet>
    ```
    

### 주의 사항

-   **서블릿 컨테이너 구성**: StandardServletMultipartResolver는 서블릿 컨테이너의 멀티파트 파서를 그대로 사용하므로, 컨테이너 구현 차이에 노출될 수 있습니다. 기본적으로 모든 multipart/ 콘텐츠 유형을 분석하려고 하지만, 이는 모든 서블릿 컨테이너에서 지원되지 않을 수 있습니다.
-   **설정 및 옵션**: 이 리졸버는 다양한 구성 옵션을 제공하므로, 필요에 따라 설정할 수 있습니다. 자세한 내용은 StandardServletMultipartResolver의 Javadoc에서 확인할 수 있습니다.

### 요약

-   **MultipartResolver**는 파일 업로드를 처리하는 전략입니다.
-   멀티파트 파일 업로드를 위해서는 **multipartResolver** 빈을 선언해야 하며, Servlet 설정이 필요합니다.
-   Java 코드나 web.xml 파일에서 멀티파트 파싱을 설정할 수 있습니다.
-   **StandardServletMultipartResolver**는 서블릿 컨테이너의 멀티파트 파서를 사용하여 요청을 분석합니다.

이러한 설정을 통해 Spring MVC 애플리케이션에서 파일 업로드 기능을 손쉽게 구현할 수 있습니다.

## Logging

Spring MVC에서의 로깅은 애플리케이션의 상태를 추적하고 디버깅하는 데 중요한 역할을 합니다. Spring MVC는 다양한 로깅 레벨을 제공하며, 그 중 **DEBUG**와 **TRACE** 레벨의 로깅에 대해 설명하겠습니다.

### DEBUG-Level Logging

-   **목적**: DEBUG 레벨의 로깅은 간결하고 인간 친화적이며, 유용한 정보 조각들을 중심으로 구성됩니다. 이러한 정보는 문제를 해결하는 데 유용한 반복적으로 사용할 수 있는 정보를 제공합니다.
-   **특징**:
    -   로그 메시지는 최소화되어 가독성이 좋습니다.
    -   자주 발생하는 상황에 대한 정보를 강조합니다.
    -   특정 문제를 디버깅할 때 도움이 되는 정보에 집중합니다.

### TRACE-Level Logging

-   **목적**: TRACE 레벨의 로깅은 DEBUG와 유사한 원칙을 따르지만, 더욱 상세한 정보를 제공합니다. 이 레벨은 특정 문제를 해결하기 위한 디버깅에 유용하게 사용됩니다.
-   **특징**:
    -   TRACE 로그는 DEBUG 로그보다 더 많은 세부 정보를 포함할 수 있습니다.
    -   디버깅이 필요한 모든 상황에서 사용될 수 있습니다.

### 좋은 로깅

좋은 로깅은 로그를 실제로 사용해 본 경험에서 비롯됩니다. 로그가 목표에 부합하지 않거나 문제가 발생한 경우, 사용자들은 이를 피드백으로 제공할 수 있습니다. 이는 시스템을 개선하는 데 도움이 됩니다.

### Sensitive Data (민감한 데이터)

-   DEBUG 및 TRACE 레벨의 로깅은 민감한 정보를 기록할 수 있습니다. 이로 인해 요청 매개변수 및 헤더는 기본적으로 마스킹됩니다.
-   만약 이러한 세부 정보를 전체적으로 기록하고자 한다면, DispatcherServlet의 enableLoggingRequestDetails 속성을 사용하여 설정을 활성화해야 합니다.

### 예제: Java Configuration

아래는 Java 설정을 통해 요청 세부 정보를 기록하도록 설정하는 예제입니다:

```
public class MyInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return ... ; // Root config classes를 반환합니다.
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return ... ; // Servlet config classes를 반환합니다.
    }

    @Override
    protected String[] getServletMappings() {
        return ... ; // Servlet mappings를 반환합니다.
    }

    @Override
    protected void customizeRegistration(ServletRegistration.Dynamic registration) {
        registration.setInitParameter("enableLoggingRequestDetails", "true"); // 요청 세부 정보 로깅 활성화
    }
}
```

-   **설명**:
    -   MyInitializer 클래스는 Spring의 AbstractAnnotationConfigDispatcherServletInitializer를 확장하여 Servlet 초기화를 설정합니다.
    -   customizeRegistration 메서드에서 enableLoggingRequestDetails 초기화 매개변수를 "true"로 설정하여 요청 세부 정보의 로깅을 활성화합니다.

### 요약

-   **DEBUG**와 **TRACE** 레벨의 로깅은 문제를 해결하고 애플리케이션 상태를 모니터링하는 데 유용한 도구입니다.
-   요청 매개변수 및 헤더는 기본적으로 마스킹되어 있지만, 이를 변경하여 전체 정보를 로깅할 수 있습니다.
-   이를 위해 DispatcherServlet의 enableLoggingRequestDetails 속성을 설정해야 합니다.

이러한 설정을 통해 Spring MVC 애플리케이션에서 효과적인 로깅 전략을 구현하고, 문제를 더 쉽게 추적하고 해결할 수 있습니다.