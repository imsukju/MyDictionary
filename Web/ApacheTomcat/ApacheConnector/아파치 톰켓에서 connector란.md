### Tomcat의 Connector란 무엇인가?

Tomcat은 웹 서버이자 서블릿 컨테이너로서 클라이언트가 보낸 요청을 수신하고, 이를 Java 기반 웹 애플리케이션(서블릿 및 JSP)으로 전달하여 처리한 후 응답을 다시 클라이언트로 보내는 역할을 합니다. 이때, 클라이언트와 서버 간의 통신을 가능하게 해주는 핵심 요소가 **Connector**입니다. Connector는 **Tomcat이 클라이언트의 요청을 받아들이고, 이를 서버 내부의 처리 엔진인 Catalina로 전달하는** 역할을 담당하며, 네트워크와 Tomcat 사이의 **게이트웨이 역할**을 수행합니다.

#### 1. Tomcat의 아키텍처 이해
Tomcat은 여러 컴포넌트가 유기적으로 결합된 구조로 이루어져 있으며, 각 컴포넌트가 특정한 역할을 수행합니다. 특히 Tomcat의 주요 구성 요소인 `Catalina`, `Connector`, 그리고 `Container`의 역할이 중요합니다.

- **Catalina**: Tomcat의 내부 이름으로, 전체 서블릿 컨테이너 시스템을 나타내는 이름입니다. Tomcat의 모든 서블릿 처리 로직은 Catalina 내부에서 이루어지며, 여기에는 엔진(`Engine`), 호스트(`Host`), 컨텍스트(`Context`), 래퍼(`Wrapper`) 같은 계층적인 구조가 포함됩니다.
  
- **Engine**: Catalina 내부의 구성 요소로, 여러 `Connector`에서 전달된 요청을 관리하고 이를 해당 애플리케이션으로 전달합니다. Engine은 Tomcat의 메인 서블릿 엔진이며, 하나의 애플리케이션 서버 내에서 여러 `Host`를 가질 수 있습니다.

- **Connector**: Tomcat과 외부 클라이언트(브라우저 또는 다른 애플리케이션) 간의 네트워크 연결을 담당하며, 클라이언트의 HTTP, HTTPS, AJP 요청을 받아들여 이를 `Engine`으로 전달합니다. 또한 `Engine`에서 처리된 응답을 클라이언트로 다시 전달하여 통신을 완료합니다.

- **Container**: Tomcat의 요청을 처리하는 계층적인 구조를 의미합니다. `Engine`, `Host`, `Context`, `Wrapper`와 같은 여러 계층으로 이루어져 있으며, 각각의 Container는 요청을 적절히 분배하고 관리합니다.

이처럼 `Connector`는 Tomcat의 네트워크 입출력(I/O) 작업을 담당하는 핵심 요소로, Tomcat의 성능 및 네트워크 처리 효율성에 직접적인 영향을 미칩니다.

#### 2. Connector의 구성 요소
Tomcat의 Connector는 다음과 같은 주요 구성 요소로 이루어져 있습니다:

- **Protocol Handler**: 클라이언트 요청이 어떤 프로토콜(HTTP/1.1, HTTP/2, HTTPS, AJP 등)로 전달될지를 정의하고, 해당 프로토콜을 처리하는 저수준 I/O 기능을 제공합니다. 이를 통해 각 프로토콜에 맞는 네트워크 연결 방식을 구현합니다. 예를 들어 `Http11NioProtocol`, `Http11AprProtocol`, `AjpProtocol` 등의 다양한 Protocol Handler가 존재합니다.

- **Processor**: 프로토콜 핸들러가 받아들인 클라이언트 요청을 분석하고, HTTP 메서드(GET, POST 등), 헤더 정보, URI, 파라미터 등을 추출하여 Tomcat의 서블릿 컨테이너인 Catalina로 전달합니다. 이 단계에서 요청이 올바르게 분석되지 않으면 올바른 자원으로 요청이 전달되지 않으므로 중요한 역할을 담당합니다.

- **Adapter**: `Connector`와 `Catalina` 엔진 간의 통신을 담당하는 중재자 역할을 하며, `Catalina`가 요청을 처리한 후 `Connector`로 전달된 응답을 적절한 형식으로 다시 `Protocol Handler`로 보내어 클라이언트에게 전송할 수 있도록 합니다. 즉, `Catalina`의 내부 객체와 `Connector`가 서로 데이터를 주고받을 수 있도록 해주는 일종의 변환기 역할을 합니다.

이러한 구성 요소들이 함께 동작하여 Tomcat의 네트워크 요청을 처리하고, 애플리케이션 서버의 응답을 클라이언트에게 전달하게 됩니다.

#### 3. Connector의 역할과 작동 원리
Tomcat의 Connector는 클라이언트와의 네트워크 연결을 처리하고, Catalina 엔진으로 요청을 전달하는 중요한 역할을 수행합니다. 이를 이해하기 위해 전체 작업 흐름을 다음과 같이 설명할 수 있습니다:

1. **네트워크 포트 바인딩**: `Connector`는 특정 포트에 바인딩되어 클라이언트의 요청을 수신할 준비를 합니다. 기본적으로 Tomcat은 HTTP 요청을 `8080` 포트에서 기다립니다. 예를 들어, `http://localhost:8080`으로 접속하면 `8080` 포트에 바인딩된 `Connector`가 요청을 수신하게 됩니다.

2. **프로토콜 핸들링**: `Connector`는 클라이언트가 어떤 프로토콜을 사용하여 요청을 보냈는지 식별하고, 해당 프로토콜에 맞는 핸들러(`Protocol Handler`)를 통해 요청을 파싱합니다. 예를 들어, `HTTP/1.1` 요청은 `Http11NioProtocol` 핸들러가 처리하고, `AJP` 프로토콜은 `AjpProtocol` 핸들러가 처리하게 됩니다.

3. **요청 분석 및 처리**: 파싱된 요청은 `Processor`를 통해 분석되며, HTTP 헤더, 메서드(GET, POST 등), URI, 쿼리 파라미터 등이 추출됩니다. 그런 다음, 해당 정보를 `Adapter`를 통해 Tomcat의 Catalina 엔진으로 전달합니다.

4. **서블릿 요청 처리**: `Catalina` 엔진은 `Engine`, `Host`, `Context`, `Wrapper` 계층을 거치면서 적절한 서블릿으로 요청을 전달하여 응답을 생성합니다.

5. **응답 처리 및 전송**: Catalina가 생성한 응답은 다시 `Adapter`를 통해 `Connector`의 `Processor`로 전달됩니다. `Processor`는 이를 기반으로 HTTP 응답 객체를 구성하고, `Protocol Handler`를 통해 네트워크를 통해 클라이언트로 응답을 전송합니다.

이러한 단계에서 Tomcat은 각 요청이 어떤 컨텍스트로 전달되어야 하는지, 서블릿이 어떻게 호출되어야 하는지를 결정하게 됩니다.

#### 4. Connector의 세부 설정
`server.xml` 파일을 통해 각 Connector의 동작 방식을 세부적으로 설정할 수 있습니다. 예를 들어:

```xml
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443"
           maxThreads="500"
           acceptCount="1000"
           maxKeepAliveRequests="100"
           compression="on"
           enableLookups="false" />
```

- **maxThreads**: Connector가 동시에 처리할 수 있는 최대 스레드 수를 지정합니다. 기본값은 `200`이지만, 많은 트래픽을 처리해야 하는 경우 이 값을 늘릴 수 있습니다.
- **acceptCount**: 최대 스레드가 모두 사용 중일 때, 대기열에 놓일 수 있는 최대 요청 수를 지정합니다.
- **maxKeepAliveRequests**: Keep-Alive로 유지될 수 있는 최대 요청 수를 정의합니다. 이 값을 초과하면 Keep-Alive가 종료됩니다.
- **compression**: HTTP 응답을 gzip 압축하여 전송할지 여부를 설정합니다.
- **enableLookups**: IP 주소를 호스트 이름으로 변환할지 여부를 설정합니다. 성능 상의 이유로 기본값은 `false`입니다.

#### 5. 다양한 Connector의 종류와 각 특성
Tomcat에서 사용할 수 있는 Connector의 주요 유형은 다음과 같습니다:

- **HTTP Connector**: 기본적으로 HTTP 요청을 처리하며, `HTTP/1.1` 또는 `HTTP/2` 프로토콜을 지원합니다.
- **AJP Connector**: `Apache HTTPD`와 같은 웹 서버와 통신하기 위해 사용되며, 성능 최적화를 위해 바이너리 포맷으로 데이터를 전달합니다.
- **APR Connector**: 네이티브 C 기반의 `Apache Portable Runtime` 라이브러리를 사용하여 성능을 극대화하고, 네이티브 I/O 처리 및 SSL 핸들링 기능을 제공합니다.

### 결론
Tomcat의 `Connector`는 클라이언트와 서버를 연결하는 중요한 컴포넌트로, 각 I/O 모델과 프로토콜 설정에 따라 성능과 안정성에 큰 영향을 미칩니다. 올바른 Connector 설정을 통해 더 높은 성능과 안정성을 유지할 수 있습니다.
