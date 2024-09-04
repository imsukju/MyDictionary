### Java Agent 개요

**Java Agent**는 **Java 애플리케이션**의 실행 중 동작을 변경하거나 모니터링할 수 있는 **특별한 프로그램**입니다. 이 프로그램은 **JVM(Java Virtual Machine)**과 상호 작용하여 **클래스 로딩 시점**에 바이트코드를 조작하거나, 이미 로드된 클래스의 동작을 **런타임**에 수정할 수 있습니다. 이러한 기능은 **성능 모니터링**, **디버깅**, **프로파일링**, **코드 커버리지 분석**, **AOP(Aspect-Oriented Programming)** 구현 등 다양한 목적에 사용됩니다.

### Java Agent의 주요 특징

#### 1. **Premain 메서드**
- **Java Agent**는 **JVM이 시작될 때** `premain` 메서드를 통해 실행됩니다. 이 메서드는 JVM이 메인 애플리케이션을 실행하기 전에 호출됩니다.
- **`premain` 메서드의 형태**:
  ```java
  public static void premain(String agentArgs, Instrumentation inst)
  public static void premain(String agentArgs)
  ```
- 첫 번째 형태는 `Instrumentation` API에 접근할 수 있는 객체를 인자로 받아, 클래스 로딩 시점에서의 다양한 조작을 가능하게 합니다.

#### 2. **Instrumentation API 사용**
- **Java Agent**는 **Instrumentation API**를 통해 **클래스 로딩 과정에 개입**할 수 있습니다. 이 API는 **바이트코드**를 **변환**하거나 **재정의**할 수 있는 메서드들을 제공하며, 이를 통해 Java Agent는 **런타임에 애플리케이션의 동작**을 변경할 수 있습니다.
- 주요 메서드:
  - `addTransformer`: **클래스 로딩 시** 바이트코드를 변환하는 데 사용됩니다.
  - `redefineClasses`: **이미 로드된 클래스**를 재정의할 수 있습니다.

#### 3. **Java Agent 등록 및 실행**
- **Java Agent**는 JVM 시작 시 **`-javaagent`** 옵션을 통해 등록됩니다. 예를 들어:
  ```bash
  java -javaagent:/path/to/agent.jar -jar myapp.jar
  ```
- 이 옵션은 JVM이 시작될 때 지정된 **JAR 파일**을 Java Agent로 인식하고, 해당 에이전트의 `premain` 메서드를 호출합니다.

#### 4. **JAR 파일 구조**
- **Java Agent**는 **JAR 파일** 형태로 패키징됩니다. 이 JAR 파일의 **META-INF/MANIFEST.MF** 파일에는 `Premain-Class` 속성이 포함되어야 하며, 이 속성은 JVM이 어떤 클래스의 `premain` 메서드를 호출할지를 지정합니다.
  - 예시:
    ```zsh
    Premain-Class: com.example.MyAgent
    ```

### Java Agent의 일반적인 사용 사례

#### 1. **성능 모니터링**
- **Java Agent**는 애플리케이션의 **실행 중 성능 관련 데이터**를 수집하는 데 사용될 수 있습니다. 예를 들어, 메서드 실행 시간, 메모리 사용량 등을 추적하여 성능을 모니터링합니다.

#### 2. **프로파일링 도구**
- **프로파일링 도구**로서, Java Agent는 애플리케이션의 **실행을 프로파일링**하여 메모리 사용, 스레드 상태, CPU 사용 등의 데이터를 수집하고 분석할 수 있습니다.

#### 3. **코드 커버리지 분석**
- **코드 커버리지 도구**로서, Java Agent는 테스트 실행 중 어떤 코드가 실행되었는지를 추적하고, 이를 바탕으로 **커버리지 보고서**를 생성할 수 있습니다.

#### 4. **AOP(Aspect-Oriented Programming)**
- **AOP 구현**에서, Java Agent는 **AspectJ**와 같은 프레임워크와 함께 사용되어 **런타임에 클래스 파일을 변환**하고, 특정 메서드 호출 전후에 코드를 삽입하는 등의 기능을 제공합니다.

#### 5. **디버깅 및 로깅**
- **디버깅 도구**로, Java Agent는 애플리케이션 실행 중 **특정 이벤트나 상태를 실시간으로 로깅**하거나, **디버깅 정보를 수집**할 수 있습니다. 이를 통해 실행 중 문제를 분석하고 해결하는 데 도움을 줄 수 있습니다.

#### 요약

**Java Agent**는 Java 애플리케이션의 동작을 동적으로 제어할 수 있는 **강력한 도구**입니다. 이를 통해 개발자는 성능 최적화, 코드 분석, AOP 구현, 디버깅 등 다양한 목적을 위해 애플리케이션의 바이트코드를 조작할 수 있습니다. **Instrumentation API**와 함께 사용되는 Java Agent는 JVM 레벨에서 애플리케이션의 실행을 세밀하게 제어할 수 있는 중요한 수단을 제공합니다.


### Java Agent 예시 코드

다음은 간단한 Java Agent의 예시입니다. 이 에이전트는 모든 클래스가 로드될 때마다 그 클래스 이름을 출력합니다.

```java
import java.lang.instrument.ClassFileTransformer;
import java.lang.instrument.Instrumentation;
import java.security.ProtectionDomain;

public class SimpleAgent {
    public static void premain(String agentArgs, Instrumentation inst) {
        inst.addTransformer(new SimpleTransformer());
    }
}

class SimpleTransformer implements ClassFileTransformer {
    @Override
    public byte[] transform(ClassLoader loader, String className, Class<?> classBeingRedefined, 
                            ProtectionDomain protectionDomain, byte[] classfileBuffer) {
        System.out.println("Transforming class: " + className);
        // 바이트코드를 수정하지 않는 경우 원본 바이트코드를 반환
        return classfileBuffer;
    }
}
```

> **Java Agent**는 JVM의 클래스 로딩 과정에 개입하여 애플리케이션의 동작을 실시간으로 조작할 수 있는 도구입니다. 주로 Instrumentation API를 사용하여 바이트코드 수준에서의 수정 작업을 수행하며, 성능 모니터링, 프로파일링, 코드 커버리지 분석, AOP 구현 등 다양한 용도로 사용됩니다. Java Agent는 JVM 시작 시 \-javaagent 옵션을 통해 로드되며, 애플리케이션의 실행 전반에 걸쳐 중요한 역할을 할 수 있습니다.


### 대표적인 Java Agents

#### 1. **Java Flight Recorder (JFR)**
- **용도**: 성능 모니터링 및 프로파일링
- **설명**: JFR은 JDK에 내장된 성능 모니터링 도구로, Java 애플리케이션 실행 중 발생하는 다양한 이벤트를 기록하고 분석하는 데 사용됩니다. JFR은 매우 낮은 오버헤드로 성능 데이터를 수집할 수 있어, 프로덕션 환경에서 성능 문제를 분석하는 데 매우 유용합니다.
- **특징**: JDK에 내장되어 있어 별도의 설치가 필요 없으며, `-XX:StartFlightRecording` 옵션으로 활성화할 수 있습니다.

#### 2. **JaCoCo (Java Code Coverage Library)**
- **용도**: 코드 커버리지 분석
- **설명**: JaCoCo는 Java 애플리케이션의 테스트 커버리지를 측정하는 도구로, 테스트 실행 중 어떤 코드가 실행되었는지를 추적하여 코드 커버리지 보고서를 생성합니다.
- **특징**: Maven, Gradle 등 빌드 도구와 쉽게 통합되며, CI/CD 파이프라인에서 코드 커버리지를 자동으로 측정하고 보고할 수 있습니다.

#### 3. **New Relic**
- **용도**: 애플리케이션 성능 모니터링(APM)
- **설명**: New Relic은 클라우드 기반의 애플리케이션 성능 모니터링 도구로, Java Agent를 통해 애플리케이션의 성능을 모니터링하고 문제를 진단할 수 있습니다. 메서드 호출, 데이터베이스 쿼리, 외부 API 호출 등을 모니터링하여 실시간 성능 데이터를 수집합니다.
- **특징**: 다양한 대시보드와 실시간 성능 모니터링 기능을 제공하며, 클라우드 서비스와 통합이 용이합니다.

#### 4. **AspectJ**
- **용도**: AOP(Aspect-Oriented Programming)
- **설명**: AspectJ는 Java에서 AOP를 구현하는 프레임워크로, Java Agent를 통해 런타임에 클래스 파일을 위빙(Weaving)할 수 있습니다. 이를 통해 특정 포인트컷(Pointcut)에 따라 메서드 호출 전후에 코드를 삽입하는 등의 작업을 수행할 수 있습니다.
- **특징**: 로드 타임 위빙(LTW)을 지원하며, 복잡한 비즈니스 로직에서 횡단 관심사를 분리하여 관리할 수 있습니다.

#### 5. **Byte Buddy**
- **용도**: 런타임 바이트코드 조작
- **설명**: Byte Buddy는 Java 클래스의 바이트코드를 런타임에 동적으로 생성하거나 변환하는 라이브러리입니다. Java Agent를 통해 클래스 로딩 시점에서 바이트코드를 변환하는 작업을 쉽게 수행할 수 있습니다.
- **특징**: 다양한 프레임워크와 통합이 용이하며, 바이트코드를 조작하는 데 필요한 강력한 API를 제공합니다.

#### 6. **BTrace**
- **용도**: 런타임 디버깅 및 트레이싱
- **설명**: BTrace는 Java 애플리케이션의 런타임 동작을 실시간으로 추적할 수 있는 도구입니다. BTrace의 Java Agent는 애플리케이션 코드의 변경 없이 다양한 이벤트를 실시간으로 추적할 수 있습니다.
- **특징**: 매우 가볍고, 애플리케이션 성능에 최소한의 영향을 주며, 실시간 모니터링에 적합합니다.

#### 7. **Glowroot**
- **용도**: 애플리케이션 성능 모니터링(APM)
- **설명**: Glowroot는 오픈 소스 APM 도구로, Java Agent를 통해 애플리케이션의 성능을 모니터링합니다. Glowroot는 스레드 프로파일링, 트랜잭션 추적, SQL 분석 등 다양한 기능을 제공합니다.
- **특징**: 설치와 설정이 간편하며, 경량화된 APM 솔루션으로 많은 개발자와 팀에서 애용됩니다.

### Java Agent 사용 예제

아래 예제에서는 **SimpleAgent**라는 Java Agent를 사용해, JVM에서 로드되는 모든 클래스의 이름을 출력하는 간단한 기능을 구현합니다. 이 예제는 Java Agent의 기본적인 사용 방법을 보여주며, 실제 애플리케이션에서 다양한 목적을 위해 바이트코드 조작을 적용할 수 있습니다.

#### 1. **Java Agent 컴파일 및 JAR 파일 생성**

1.1. **SimpleAgent.java 파일로 저장**
```java
public class SimpleAgent {
    public static void premain(String agentArgs, Instrumentation inst) {
        inst.addTransformer(new SimpleTransformer());
    }
}

class SimpleTransformer implements ClassFileTransformer {
    @Override
    public byte[] transform(ClassLoader loader, String className, Class<?> classBeingRedefined, ProtectionDomain protectionDomain, byte[] classfileBuffer) {
        System.out.println("Transforming class: " + className);
        return classfileBuffer;  // 바이트코드를 수정하지 않고 그대로 반환
    }
}
```

1.2. **컴파일**
```bash
javac SimpleAgent.java
```

1.3. **JAR 파일 생성**
- `MANIFEST.MF` 파일 작성:
```plaintext
Premain-Class: SimpleAgent
```

- JAR 파일 생성 명령:
```bash
jar cmf MANIFEST.MF simple-agent.jar SimpleAgent.class SimpleTransformer.class
```

#### 2. **Java Agent를 사용하는 애플리케이션 실행**

2.1. **간단한 Java 애플리케이션 작성**
```java
public class MyApplication {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

2.2. **애플리케이션 컴파일**
```bash
javac MyApplication.java
```

2.3. **Java Agent와 함께 애플리케이션 실행**
```bash
java -javaagent:/path/to/simple-agent.jar -cp . MyApplication
```

#### 3. **실행 결과**
애플리케이션 실행 시, `SimpleAgent`가 로드되고 `MyApplication` 클래스를 포함한 모든 클래스가 로드될 때마다 클래스 이름이 출력됩니다.

```plaintext
Transforming class: MyApplication
Hello, World!
```

### 요약

- **Java Agent**는 JVM에서 실행되는 Java 애플리케이션의 동작을 실시간으로 모니터링하거나 수정할 수 있는 강력한 도구입니다.
- 다양한 Java Agent들이 성능 모니터링, 코드 커버리지 분석, AOP 구현, 디버깅 등의 작업에 사용됩니다.
- 예제에서는 Java Agent를 사용하여 JVM에서 로드되는 모든 클래스의 이름을 출력하는 기본적인 기능을 구현하였습니다.

이 예제를 통해 Java Agent의 기본 사용 방법과 개념을 이해할 수 있으며, 실제로 애플리케이션에서 Java Agent를 활용하여 다양한 목적을 달성할 수 있습니다.