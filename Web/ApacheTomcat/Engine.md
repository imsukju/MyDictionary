Apache Tomcat의 **Engine**은 톰캣의 내부 구조에서 중요한 역할을 하는 서블릿 컨테이너로, 톰캣의 **Service**와 연관된 다양한 가상 호스트(Host)에 대한 요청을 관리하고 처리하는 기능을 수행합니다. 아래는 Engine의 주요 개념과 동작 과정을 상세히 정리한 내용입니다.

## 1. Engine의 주요 역할

Engine은 여러 **Connector**에서 전달된 요청을 처리하는 **Service**의 핵심 구성 요소입니다. 이 구성 요소는 클라이언트로부터 받은 요청을 적절한 **가상 호스트(Host)**와 **웹 애플리케이션(Context)**에 라우팅하고, 각 요청을 처리한 결과를 다시 클라이언트에게 전달합니다.

### 1.1 요청 라우팅

- **Engine**의 가장 기본적인 역할은 **클라이언트로부터 전달받은 HTTP 요청**을 올바른 **Host(가상 호스트)**로 전달하는 것입니다.
- 하나의 **Engine**에는 여러 개의 **Host**가 존재할 수 있으며, 각 **Host**는 서로 다른 도메인 또는 서브도메인에 대응합니다.

### 1.2 서블릿 실행

- **Engine**은 적절한 **Host**와 **Context**를 선택하여 **서블릿**을 실행합니다.
- 각 서블릿의 실행 결과로 생성된 응답이 클라이언트에게 반환됩니다.

### 1.3 구성 요소 통합

- **Engine**은 톰캣의 다양한 구성 요소들을 결합하여 전체 서블릿 실행 환경을 제공합니다.
- 요청 수신부터 응답 반환까지의 전체 과정을 처리하며, 톰캣의 **Service** 내 모든 **Host**와 **Context**를 관리합니다.

## 2. Engine의 주요 구성 요소

Engine은 다양한 하위 컴포넌트와 함께 작동하여 클라이언트 요청을 처리합니다. 각 컴포넌트의 역할과 구조는 다음과 같습니다.

### 2.1 Host

- **Host**는 특정 도메인이나 서브도메인을 나타내는 가상 호스트입니다.
- 하나의 **Engine**은 여러 개의 **Host**를 가질 수 있으며, 각 **Host**는 서로 다른 도메인에 대응하는 웹 애플리케이션을 관리합니다.
- **server.xml** 파일 내의 `<Host>` 요소를 통해 설정됩니다.
  
  ```xml
  <Host name="www.example.com" appBase="webapps" unpackWARs="true" autoDeploy="true">
      <Context path="" docBase="exampleApp" reloadable="true"/>
  </Host>
  ```

### 2.2 Context

- **Context**는 **Host** 내에서 개별 **웹 애플리케이션**을 나타냅니다.
- **Host**가 도메인 단위의 요청을 관리한다면, **Context**는 특정 경로(예: `/app1`, `/app2`)에 대응하여 각 웹 애플리케이션을 관리합니다.
- 하나의 **Host**에는 여러 개의 **Context**가 존재할 수 있으며, 이를 통해 여러 애플리케이션을 단일 호스트에 배포할 수 있습니다.

### 2.3 Realm

- **Realm**은 **사용자 인증과 권한 부여**를 처리하는 보안 컴포넌트입니다.
- 사용자의 자격 증명(예: 사용자 이름과 비밀번호)을 확인하고, 특정 리소스에 대한 접근 권한을 부여합니다.
- **Engine** 수준에서 설정된 **Realm**은 **엔진 내부의 모든 Host와 Context에 대해 적용**됩니다.

### 2.4 Valve

- **Valve**는 요청 처리의 특정 단계에서 작동하는 **필터** 역할을 합니다.
- 엔진, 호스트, 또는 컨텍스트 수준에서 설정 가능하며, 요청이 해당 단위로 들어올 때마다 작동합니다.
- **Valve**는 보안 로깅, 인증 및 권한 부여와 같은 작업을 수행하는 데 자주 사용됩니다.

  예시:

  ```xml
  <Valve className="org.apache.catalina.valves.AccessLogValve"
         directory="logs"
         prefix="localhost_access_log"
         suffix=".txt"
         pattern="%h %l %u %t &quot;%r&quot; %s %b"/>
  ```

## 3. Engine의 동작 과정

톰캣의 **Engine**은 클라이언트의 요청을 처리하는 중요한 역할을 담당합니다. 기본적인 요청 처리 과정은 다음과 같습니다.

1. **Connector로부터 요청 수신**: 클라이언트가 HTTP 요청을 보내면, 해당 요청이 **Connector**로 들어옵니다. 이후 Connector는 요청을 **Engine**으로 전달합니다.
2. **Host 선택**: **Engine**은 요청의 **호스트 헤더(Host Header)**를 분석하여, 적절한 **가상 호스트(Host)**를 선택합니다.
3. **Context 선택**: 선택된 **Host**는 URI를 분석하여 적절한 **Context(웹 애플리케이션)**를 선택합니다.
4. **서블릿 실행**: **Context** 내에서 서블릿 컨테이너가 요청을 처리할 서블릿을 찾아 실행합니다. 서블릿은 클라이언트 요청을 처리하고 응답을 생성합니다.
5. **응답 반환**: 서블릿이 생성한 응답이 다시 **Connector**를 통해 클라이언트에게 반환됩니다.

## 4. Engine 설정

톰캣의 `server.xml` 파일에서 **Engine** 컴포넌트를 설정할 수 있습니다. 아래는 **Engine** 설정 예시입니다.

```xml
<Service name="Catalina">
  <Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" />
  <Engine name="Catalina" defaultHost="localhost">
    <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
           resourceName="UserDatabase"/>
    <Host name="localhost" appBase="webapps" unpackWARs="true" autoDeploy="true"/>
  </Engine>
</Service>
```

- `name`: 엔진의 이름을 지정합니다. 기본값은 주로 `Catalina`입니다.
- `defaultHost`: 엔진이 요청을 처리할 때 사용할 기본 호스트를 지정합니다.
- `Realm`: 인증과 권한 부여를 처리하는 **Realm**을 설정합니다.
- `Host`: 여러 **Host**를 정의하여, 도메인별로 웹 애플리케이션을 배포할 수 있습니다.

## 5. Engine의 특징 요약

- **Engine**은 톰캣의 핵심 서블릿 컨테이너로, HTTP 요청을 처리하고 서블릿 및 JSP를 실행하는 과정의 중추적 역할을 수행합니다.
- 여러 **가상 호스트(Host)**와 **웹 애플리케이션(Context)**을 처리하며, 요청을 올바른 애플리케이션으로 라우팅합니다.
- **Valve**와 **Realm**과 같은 기능을 통해 추가적인 보안 및 로깅 기능을 제공합니다.

이와 같이, Apache Tomcat의 **Engine**은 톰캣의 다양한 요청 처리 과정을 통합하여 서블릿 컨테이너의 중요한 역할을 담당하는 구성 요소입니다.