## Apache Tomcat의 Host

**Apache Tomcat**에서 **Host**는 톰캣 서버 내에서 **가상 호스트(Virtual Host)**를 나타내는 구성 요소입니다. 하나의 **Engine** 내에서 여러 **Host**를 정의할 수 있으며, 각각의 **Host**는 주로 하나의 **도메인** 또는 **서브도메인**에 대응하여, 해당 도메인에 대한 **웹 애플리케이션**을 관리하고 클라이언트의 요청을 적절한 **Context(웹 애플리케이션)**로 라우팅합니다. 이러한 가상 호스팅을 통해 여러 개의 도메인을 하나의 톰캣 서버에서 운영할 수 있습니다.

## 1. Host의 주요 역할

### 1.1 가상 호스트 역할

- **Host**는 **가상 호스트(Virtual Host)**를 나타내며, 하나의 서버 내에서 여러 도메인이나 서브도메인(예: `www.example.com`, `app.example.com`)에 대해 서로 다른 웹 애플리케이션을 관리하고 실행할 수 있습니다.
- 각 **Host**는 특정 **도메인**에 매핑되며, 해당 도메인으로 들어오는 요청을 적절한 **Context**로 라우팅합니다.

### 1.2 도메인 및 Context 관리

- **Host**는 도메인 또는 서브도메인 이름에 따라 요청을 적절한 **Context**로 전달하고, 각 Context는 웹 애플리케이션의 특정 경로(예: `/app1`, `/app2`)에 매핑되어 요청을 처리합니다.
- 여러 **Context**를 정의하여 하나의 도메인 내에서도 다양한 경로에 따라 여러 애플리케이션을 배포할 수 있습니다.

### 1.3 애플리케이션 배포 및 관리

- **Host**는 각 도메인에 대해 **웹 애플리케이션을 배포**할 디렉토리를 지정하고, 해당 애플리케이션의 **배포, 갱신, 삭제**를 관리합니다.
- `autoDeploy`와 같은 속성을 통해 **자동 배포** 및 **자동 갱신**을 설정하여 톰캣 서버가 실행 중일 때도 애플리케이션을 즉시 배포하고 업데이트할 수 있습니다.

## 2. Host의 주요 구성 요소와 속성

**Host**는 여러 속성과 설정을 통해 도메인에 맞는 세부 설정을 제공합니다. 톰캣의 `server.xml` 파일 내에서 **<Host>** 태그를 통해 설정할 수 있습니다. 주요 속성 및 역할은 다음과 같습니다.

### 2.1 name

- **name** 속성은 **Host**가 처리할 **도메인 이름**을 지정합니다.
- 예를 들어, `www.example.com`과 같은 도메인 이름을 **name** 속성에 지정하면, 톰캣은 해당 도메인으로 들어오는 요청을 이 Host가 처리하도록 설정합니다.

  **예시**:
  ```xml
  <Host name="www.example.com" appBase="webapps/example" unpackWARs="true" autoDeploy="true" />
  ```

### 2.2 appBase

- **appBase** 속성은 이 **Host**에 배포된 **웹 애플리케이션이 위치할 디렉토리**를 지정합니다.
- 기본적으로 톰캣은 **`webapps`** 디렉토리를 사용하지만, 각 Host마다 다른 경로를 지정할 수 있습니다.
- **절대 경로**나 **상대 경로**로 설정할 수 있으며, 상대 경로는 톰캣의 **설치 디렉토리**를 기준으로 합니다.

  **예시**:
  ```xml
  <Host name="www.example.com" appBase="webapps/example" unpackWARs="true" autoDeploy="true" />
  ```

### 2.3 unpackWARs

- **unpackWARs** 속성은 **WAR 파일**이 배포될 때 이를 **자동으로 압축 해제**할지 여부를 지정합니다.
- `true`로 설정하면, WAR 파일이 **압축 해제**되어 **디렉토리 형태**로 배포되고 실행됩니다.
  
  **예시**:
  ```xml
  <Host name="www.example.com" appBase="webapps/example" unpackWARs="true" />
  ```

### 2.4 autoDeploy

- **autoDeploy** 속성은 **Host**가 **실행 중**일 때, **새로운 웹 애플리케이션**이 배포되거나 업데이트될 경우 이를 **자동으로 감지하고 배포할지**를 지정합니다.
- `true`로 설정하면, 톰캣이 **실행 중**일 때도 새로운 애플리케이션을 **자동으로 배포**하고 적용할 수 있습니다.

  **예시**:
  ```xml
  <Host name="www.example.com" appBase="webapps/example" autoDeploy="true" />
  ```

### 2.5 deployOnStartup

- **deployOnStartup** 속성은 톰캣이 **시작될 때** 이 **Host**에 있는 **모든 애플리케이션을 자동으로 배포**할지 여부를 결정합니다.
- `true`로 설정하면, 톰캣이 시작할 때 **appBase 디렉토리에 있는 모든 애플리케이션**이 자동으로 배포됩니다.

  **예시**:
  ```xml
  <Host name="www.example.com" appBase="webapps/example" deployOnStartup="true" />
  ```

### 2.6 Context

- **Context**는 **Host** 내에서 **각 웹 애플리케이션을 정의**하는 요소로, 여러 **Context**가 하나의 **Host** 내에서 존재할 수 있습니다.
- 각 **Context**는 **웹 애플리케이션의 특정 경로**에 매핑되며, 이를 통해 도메인 내에서 다양한 애플리케이션을 실행할 수 있습니다.

  **예시**:
  ```xml
  <Host name="www.example.com" appBase="webapps/example">
      <Context path="/" docBase="ROOT" reloadable="true"/>
      <Context path="/app1" docBase="app1" reloadable="true"/>
  </Host>
  ```

  - `path`: 각 **Context**의 **경로**를 지정합니다. 루트 애플리케이션의 경우 경로는 `/`로 지정됩니다.
  - `docBase`: **웹 애플리케이션**의 **실제 위치**를 지정합니다.
  - `reloadable`: `true`로 설정되면, 클래스 파일이 변경될 때 애플리케이션이 **자동으로 다시 로드**됩니다.

### 2.7 Alias

- **Alias**는 하나의 **Host**에 여러 **도메인 이름**을 매핑할 수 있게 해주는 설정입니다.
- 이를 통해 여러 도메인이나 서브도메인을 동일한 **Host**가 처리할 수 있습니다.

  **예시**:
  ```xml
  <Host name="www.example.com" appBase="webapps/example" unpackWARs="true" autoDeploy="true">
      <Alias>example.com</Alias>
      <Alias>www.alias-example.com</Alias>
  </Host>
  ```

  위 설정에서는 `www.example.com`, `example.com`, `www.alias-example.com` 도메인들이 모두 같은 **Host**에서 처리됩니다.

## 3. Host의 동작 과정

1. **도메인 이름으로 요청 수신**:
   - 클라이언트가 특정 **도메인**(예: `www.example.com`)으로 요청을 보낼 때, 톰캣은 **도메인 이름**을 기반으로 적절한 **Host**를 선택합니다.

2. **Context로 요청 전달**:
   - 톰캣이 **Host**를 선택한 후, **URL 경로**에 따라 적절한 **Context**(웹 애플리케이션)로 요청을 전달합니다.
   - 예를 들어, `http://www.example.com/app1`로 요청이 들어오면, Host 내에서 `/app1` 경로에 매핑된 **Context**로 요청이 전달됩니다.

3. **서블릿 또는 JSP 실행**:
   - 선택된 **Context** 내에서 해당 요청을 처리할 **서블릿**이나 **JSP 파일**을 찾아 실행합니다.

4. **응답 반환**:
   - 처리된 결과는 클라이언트에게 **응답**으로 반환됩니다.

## 4. Host 설정의 예

아래는 톰캣의 `server.xml` 파일에서 **Host** 설정의 예시입니다:

```xml
<Engine name="Catalina" defaultHost="localhost">
    <Host name="www.example.com" appBase="webapps/example" unpackWARs="true" autoDeploy="true">
        <Alias>example.com</Alias>
        <Alias>alias.example.com</Alias>

        <!-- 루트 애플리케이션 -->
        <Context path="/" docBase="ROOT" reloadable="true"/>

        <!-- 추가 애플리케이션 -->
        <Context path="/app1" docBase="app1" reloadable="true"/>
    </Host>
</Engine>
```

### 설정 설명

- `name`: `www

.example.com`이라는 도메인에 대응하는 **Host**를 정의합니다.
- `appBase`: 이 **Host**에 배포된 애플리케이션이 위치할 **디렉토리**를 지정합니다.
- `unpackWARs`와 `autoDeploy`: 새로운 **WAR 파일**이 자동으로 배포되고 **압축 해제**됩니다.
- `Alias`: 여러 도메인 또는 서브도메인에 대해 동일한 **Host**가 요청을 처리할 수 있도록 설정합니다.
- `Context`: 루트 경로 `/`와 `/app1` 경로에 대해 각각의 애플리케이션이 배포되어 있습니다.

이와 같이, **Host**는 톰캣 서버 내에서 **도메인별로 웹 애플리케이션을 관리**하고 **요청을 라우팅**하는 중요한 역할을 수행합니다. 이를 적절하게 설정하면, 하나의 톰캣 인스턴스에서 **여러 도메인과 애플리케이션**을 효과적으로 관리할 수 있습니다.