## Apache Tomcat의 Context

**Apache Tomcat**에서 **Context**는 개별 웹 애플리케이션의 실행 환경을 나타내는 중요한 구성 요소입니다. 톰캣 서버 내에서 **하나의 웹 애플리케이션**에 대한 모든 설정과 동작 방식을 관리하며, **각 애플리케이션의 URL 경로**에 대한 매핑을 담당합니다. Context는 각 웹 애플리케이션의 **서블릿, JSP, 정적 리소스** 등을 관리하고, **애플리케이션의 배포, 로딩, 초기화, 종료** 등의 과정에서 중요한 역할을 합니다.

## 1. Context의 주요 역할

1. **웹 애플리케이션 매핑**:
   - **Context**는 특정 **URL 경로(Context Path)**에 웹 애플리케이션을 매핑합니다. 예를 들어, `Context Path`가 `/myapp`인 경우, 클라이언트가 `http://localhost:8080/myapp`으로 요청을 보낼 때 해당 애플리케이션이 이를 처리하게 됩니다.
   
2. **애플리케이션 배포**:
   - Context는 **WAR 파일** 또는 **디렉토리 형태**로 배포된 웹 애플리케이션을 관리하며, 해당 애플리케이션의 서블릿, JSP, 정적 파일 등의 리소스를 처리합니다.

3. **설정 관리**:
   - **초기화 매개변수, 보안 설정, 로깅 설정** 등 다양한 설정을 통해 애플리케이션의 동작 방식을 제어합니다.
   
4. **재배포 및 핫 디플로이**:
   - 톰캣의 **Context**는 새로운 애플리케이션이 배포되거나 업데이트될 때 자동으로 애플리케이션을 다시 로드하고, 변경 사항을 즉시 반영할 수 있습니다. 이를 통해 개발 단계에서 변경된 애플리케이션을 빠르게 테스트할 수 있습니다.

## 2. Context의 구조와 구성 요소

**Context**는 다양한 설정 요소와 옵션을 포함하여 애플리케이션의 실행 환경을 정의합니다. **`server.xml`** 또는 **`context.xml`** 파일에서 설정되며, 개별 웹 애플리케이션의 **`META-INF/context.xml`** 파일에서도 정의할 수 있습니다.

### 2.1 주요 속성

#### 2.1.1 `path`
- **Context Path**는 웹 애플리케이션이 매핑된 **URL 경로**를 정의합니다.
- 예를 들어, `path` 속성이 `/myapp`인 경우, 클라이언트가 `http://localhost:8080/myapp`으로 요청을 보내면, 해당 경로에 매핑된 애플리케이션이 실행됩니다.
- **루트 애플리케이션**의 경우, `path`는 빈 문자열("")로 설정되며, 이는 기본 웹 애플리케이션을 나타냅니다.
  
  **예시**:
  ```xml
  <Context path="/myapp" docBase="myapp" reloadable="true"/>
  ```

#### 2.1.2 `docBase`
- **DocBase**는 웹 애플리케이션의 **실제 파일 시스템 경로** 또는 **WAR 파일 경로**를 지정하는 속성입니다.
- 톰캣이 이 경로를 기준으로 **서블릿, JSP, 정적 리소스** 등을 로드하여 애플리케이션을 실행합니다.

  **예시**:
  ```xml
  <Context path="/myapp" docBase="myapp" reloadable="true"/>
  ```

#### 2.1.3 `reloadable`
- **reloadable** 속성은 `true`로 설정되면, **애플리케이션의 클래스 파일**이 변경될 때 톰캣이 자동으로 애플리케이션을 **다시 로드**하도록 합니다.
- 개발 환경에서 유용하지만, **프로덕션 환경**에서는 성능 문제를 초래할 수 있으므로 권장되지 않습니다.

  **예시**:
  ```xml
  <Context path="/myapp" docBase="myapp" reloadable="true"/>
  ```

#### 2.1.4 `crossContext`
- **crossContext**는 다른 **Context(웹 애플리케이션)** 간에 **요청을 전달**할 수 있게 해주는 설정입니다.
- **`crossContext`**가 `true`로 설정된 경우, 애플리케이션 간의 상호 호출이 가능해지며, 이를 통해 한 애플리케이션에서 다른 애플리케이션으로 요청을 포워딩할 수 있습니다.

  **예시**:
  ```xml
  <Context path="/myapp" docBase="myapp" crossContext="true"/>
  ```

#### 2.1.5 `privileged`
- **privileged** 속성은 `true`로 설정되면, 해당 **Context**가 **톰캣의 시스템 기능에 접근**할 수 있도록 권한을 부여합니다.
- 주로 **관리 콘솔**이나 **보안 애플리케이션**에서 사용됩니다.

  **예시**:
  ```xml
  <Context path="/manager" docBase="manager" privileged="true"/>
  ```

## 3. Context 설정 방식

톰캣의 **Context**는 다양한 방식으로 설정할 수 있습니다. 설정 파일의 위치에 따라 Context가 정의되고 적용되는 범위가 달라집니다.

### 3.1 `server.xml`에서 설정

- 톰캣의 `server.xml` 파일에서 **<Context>** 요소를 사용하여 특정 **Host**에 대한 **Context**를 정의할 수 있습니다.
- 하지만, `server.xml` 파일에서 Context를 정의하는 것은 **일반적으로 권장되지 않는 방법**입니다. 이는 톰캣 서버가 전체적으로 다시 로드되기 때문입니다.

  **예시**:
  ```xml
  <Host name="localhost" appBase="webapps">
      <Context path="/myapp" docBase="myapp" reloadable="true"/>
  </Host>
  ```

### 3.2 `conf/context.xml`에서 설정

- 톰캣의 전역 설정 파일인 **`conf/context.xml`**에서 설정하면, **모든 웹 애플리케이션에 공통적으로 적용**됩니다.
- 이 설정은 개별 애플리케이션이 별도의 Context 설정을 갖지 않는 한 적용됩니다.

  **예시**:
  ```xml
  <Context>
      <WatchedResource>WEB-INF/web.xml</WatchedResource>
      <WatchedResource>${catalina.base}/conf/web.xml</WatchedResource>
  </Context>
  ```

### 3.3 `META-INF/context.xml`에서 설정

- 가장 **일반적인 설정 방법**은 각 웹 애플리케이션의 **`META-INF`** 디렉토리 내에 **`context.xml`** 파일을 배치하는 것입니다.
- 이를 통해 애플리케이션마다 **고유한 Context 설정**을 정의할 수 있으며, 톰캣이 자동으로 해당 설정을 감지하고 적용합니다.

  **디렉토리 구조 예시**:
  ```
  myapp/
  ├── META-INF/
  │   └── context.xml
  ├── WEB-INF/
  │   └── web.xml
  ├── index.jsp
  ```

  **context.xml 파일 예시**:
  ```xml
  <Context reloadable="true">
      <Resource name="jdbc/mydb"
                auth="Container"
                type="javax.sql.DataSource"
                maxTotal="100"
                maxIdle="30"
                maxWaitMillis="10000"
                username="dbuser"
                password="dbpassword"
                driverClassName="com.mysql.jdbc.Driver"
                url="jdbc:mysql://localhost:3306/mydb"/>
  </Context>
  ```

  위 예시에서는 **데이터베이스 연결 풀**을 정의하여, 애플리케이션 내에서 JNDI를 사용하여 데이터베이스에 접근할 수 있도록 설정하고 있습니다.

## 4. Context의 동작 과정

1. **클라이언트 요청 수신**:
   - 클라이언트가 특정 경로로 요청을 보내면, 해당 경로에 매핑된 **Context**가 요청을 처리하게 됩니다.
   
2. **서블릿 또는 JSP 실행**:
   - **Context**는 요청이 들어오면 해당 경로에 있는 **서블릿**이나 **JSP 파일**을 찾아 실행합니다.

3. **응답 반환**:
   - 서블릿 또는 JSP가 처리한 결과를 Context가 **클라이언트에게 응답**으로 반환합니다.

## 5. Context의 추가 기능

### 5.1 WatchedResource

- **WatchedResource**는 특정 **파일이나 디렉토리의 변경 사항**을 감지하여 **Context**를 **다시 로드**할 수 있게 합니다.
- 주로 `WEB-INF/web.xml`과 같은 구성 파일이 변경될 때 자동으로 Context가 다시 로드되도록 설정합니다.

  **예시**:
  ```xml
  <Context>
      <WatchedResource>WEB-INF/web.xml</WatchedResource>
  </Context>
  ```

### 5.2 Resource 정의

- **Context**는 **데

이터베이스 연결 풀**, **파일 저장소** 등의 **리소스**를 정의할 수 있습니다.
- 이를 통해 JNDI(Java Naming and Directory Interface)를 사용하여 애플리케이션 내에서 특정 리소스를 손쉽게 참조할 수 있습니다.

  **예시**:
  ```xml
  <Resource name="jdbc/mydb" auth="Container" type="javax.sql.DataSource"
            maxTotal="100" maxIdle="30" maxWaitMillis="10000"
            username="dbuser" password="dbpassword"
            driverClassName="com.mysql.jdbc.Driver"
            url="jdbc:mysql://localhost:3306/mydb"/>
  ```

이와 같이 **Context**는 Apache Tomcat에서 개별 웹 애플리케이션의 동작 방식을 세부적으로 제어하고 설정할 수 있는 중요한 구성 요소입니다. 각 설정 파일과 옵션을 적절히 조합하여 애플리케이션의 **성능**, **보안**, **확장성**을 최적화할 수 있습니다.