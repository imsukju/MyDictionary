### TDD (Test-Driven Development)란 무엇인가?

**테스트 주도 개발(TDD)**는 소프트웨어 개발 방법론 중 하나로, 코드를 작성하기 전에 테스트 케이스를 먼저 작성하는 방식입니다. TDD는 개발자가 작성한 코드가 정확하게 작동하는지 보장하기 위해 테스트를 중심으로 개발 프로세스를 조직합니다. 이 방법론은 소프트웨어의 높은 품질을 유지하고, 코드의 유지보수성을 향상시키는 데 매우 유용합니다.

### TDD의 기본 사이클: Red-Green-Refactor  

TDD는 보통 **Red-Green-Refactor**라는 세 가지 단계를 반복하며 진행됩니다.

1.  **Red (실패하는 테스트 작성)**:  
    -   개발자는 먼저 테스트 케이스를 작성합니다. 이 테스트는 아직 작성되지 않은 기능에 대한 것이므로, 실행 시 당연히 실패하게 됩니다. 이 단계에서의 실패는 코드가 아직 요구사항을 충족하지 못한다는 것을 의미합니다.
2.  **Green (코드 작성 및 테스트 통과)**:  
    -   테스트를 통과시키기 위해 필요한 최소한의 코드를 작성합니다. 목표는 테스트가 성공하도록 하는 것이며, 이 단계에서는 간단하게 테스트를 통과시키는 데 집중합니다. 코드가 정상적으로 작동해 테스트가 통과되면, 초록색 신호가 나타납니다.
3.  **Refactor (코드 개선)**:  
    -   테스트가 통과된 후, 코드의 품질을 개선하기 위해 리팩토링을 수행합니다. 이 과정에서 코드의 구조를 개선하거나 중복을 제거하며, 성능을 최적화할 수도 있습니다. 중요한 점은 리팩토링 중에도 모든 테스트가 계속 통과해야 한다는 것입니다.

이 사이클을 반복함으로써, 개발자는 점진적으로 기능을 추가하면서 코드의 정확성과 품질을 유지할 수 있습니다.

### TDD의 장점  

1.  **버그 예방**:  
    -   TDD를 통해 코드 작성 전에 명확한 요구사항을 정의하고, 테스트를 통해 버그를 조기에 발견할 수 있습니다. 이는 유지보수 비용을 줄이고, 코드의 신뢰성을 높이는 데 기여합니다.
2.  **문서화**:  
    -   테스트 케이스 자체가 코드의 사용법과 의도를 문서화하는 역할을 합니다. 다른 개발자들이 코드의 기능을 이해하는 데 큰 도움이 됩니다.
3.  **디자인 개선**:  
    -   TDD는 코드의 설계를 개선하는 데 도움을 줍니다. 테스트 가능한 작은 단위로 코드를 작성하는 습관을 통해, 자연스럽게 모듈화된 설계가 이루어지며, 코드의 재사용성과 유지보수성이 향상됩니다.
4.  **빠른 피드백**:  
    -   테스트가 자동화되어 있기 때문에, 코드를 변경할 때마다 즉각적으로 피드백을 받을 수 있습니다. 이는 개발 속도를 높이고, 안정적인 소프트웨어 개발을 가능하게 합니다.

### TDD의 단점  
 
1.  **시간 소모**:
    -   초기에는 테스트 케이스를 작성하는 데 시간이 많이 들 수 있습니다. 이로 인해 개발 속도가 느려질 수 있지만, 장기적으로는 코드 품질과 유지보수성을 높이는 데 기여합니다.
2.  **초기 학습 곡선**:
    -   TDD를 처음 접하는 개발자들에게는 새로운 사고방식과 프로세스에 익숙해지는 데 시간이 필요합니다. 특히, 테스트 작성이 익숙하지 않은 개발자에게는 어려움이 있을 수 있습니다.
3.  **테스트 유지보수**:
    -   코드가 변경되면 관련된 테스트 케이스도 함께 수정해야 합니다. 이는 테스트 코드의 유지보수 부담을 증가시킬 수 있습니다.

### TDD의 예시  

간단한 예로, Calculator 클래스의 add 메서드를 TDD 방식으로 개발한다고 가정해 보겠습니다.

1.  **Red 단계**: 실패하는 테스트 작성  

```
@Test
void testAdd() {
    Calculator calculator = new Calculator();
    int result = calculator.add(2, 3);
    assertEquals(5, result);
}
```

1.  **Green 단계**: 최소한의 코드 작성  

```
class Calculator {
    int add(int a, int b) {
        return a + b;
    }
}
```

1.  **Refactor 단계**: 리팩토링 (이 예제에서는 간단한 코드이므로 특별한 리팩토링이 필요하지 않음)

이렇게 TDD를 적용하면, 코드를 작성하는 과정에서 점진적으로 기능이 추가되고, 코드의 안정성을 유지할 수 있습니다.

### 결론

TDD는 소프트웨어 개발의 품질을 높이고, 개발자가 작성한 코드가 예상대로 작동하는지를 지속적으로 확인할 수 있게 해주는 강력한 도구입니다. 초기에는 시간과 노력이 더 필요할 수 있지만, 장기적으로 보면 유지보수성이 높고 버그가 적은 소프트웨어를 개발하는 데 큰 도움이 됩니다.


### 엔티티 클래스
클래스 자체는 테이블과 매핑  
엔티티 클래스의 객체는 로우와 매핑

### SpringFrameWork 를 사용하는 이유
비즈니스 로직에 집중하여 S/W 생상성을 향상시키기 위해서 사용합니다.

### JDBC API
대부분 인터페이스로 이루어져 있습니다  
"com.mysql.cj.jdbc.Driver.class" 는 jdbcapi 구현한 구체


### SmipleDriverDateSource
DBC(Java Database Connectivity) API에서 데이터베이스 연결을 관리하는 방법중 하나이며
DriverManager 를 조금 더 편하게 활용하기 위하여 사용합니다

코드 작성시 차이점  
**DriverManager**
```java
//DriverManager
Connection c = DriverManager.getConnection(
    "jdbc:mysql://localhost/sbdt_db?characterEncoding=UTF-8"
   ,"root"
   ,"1234" 

);
```


**SmipleDriverDateSource**
```java
SimpleDriverDataSource dataSource = new SimpleDriverDataSource();

dataSource.setrDirverClass(com.mysql.cj.....);
dataSource.setUrl("jdbc:mysql://localhost/sbdt_db?characterEncoding=UTF-8");
dataSource.setUsername("root");
dataSource.setPassword("1234");
```

# Assertions

Assertions는 JUnit에서 테스트의 결과를 검증하기 위해 사용되는 중요한 도구입니다. Assertions는 테스트 메서드 내에서 특정 조건이 참인지 또는 거짓인지를 확인하고, 조건이 충족되지 않을 경우 테스트를 실패로 처리합니다. 이를 통해 코드가 예상대로 작동하는지 검증할 수 있습니다.

### 주요 Assertions 메서드

JUnit 5에서 제공하는 주요 Assertions 메서드와 그 역할을 설명하겠습니다.

1.  **assertEquals(expected, actual)**:
    -   두 값이 서로 같은지 비교합니다.
    -   예를 들어, assertEquals(4, 2 + 2)는 2 + 2의 결과가 4와 같은지를 확인합니다.
    -   **오버로드**: assertEquals는 두 객체의 비교 외에도 부동 소수점 숫자의 비교를 위한 버전, 커스텀 메시지를 전달할 수 있는 버전 등 다양한 오버로드된 메서드를 제공합니다.
2.  **assertNotEquals(unexpected, actual)**:
    -   두 값이 서로 같지 않은지 비교합니다.
    -   예를 들어, assertNotEquals(5, 2 + 2)는 2 + 2의 결과가 5와 같지 않음을 확인합니다.
3.  **assertTrue(condition)**:
    -   조건이 참인지 확인합니다.
    -   예를 들어, assertTrue(5 > 3)는 5 > 3이 참인지를 확인합니다.
4.  **assertFalse(condition)**:
    -   조건이 거짓인지 확인합니다.
    -   예를 들어, assertFalse(5 < 3)는 5 < 3이 거짓인지를 확인합니다.
5.  **assertNull(object)**:
    -   객체가 null인지 확인합니다.
    -   예를 들어, assertNull(someObject)는 someObject가 null인지 확인합니다.
6.  **assertNotNull(object)**:
    -   객체가 null이 아닌지 확인합니다.
    -   예를 들어, assertNotNull(someObject)는 someObject가 null이 아님을 확인합니다.
7.  **assertArrayEquals(expectedArray, actualArray)**:
    -   두 배열의 내용이 같은지 비교합니다.
    -   예를 들어, assertArrayEquals(new int\[\]{1, 2, 3}, new int\[\]{1, 2, 3})는 두 배열이 동일한지 확인합니다.
8.  **assertIterableEquals(expectedIterable, actualIterable)**:
    -   두 Iterable 객체의 요소들이 같은 순서로 동일한지 확인합니다.
    -   예를 들어, assertIterableEquals(List.of(1, 2, 3), List.of(1, 2, 3))는 두 리스트가 같은지 확인합니다.
9.  **assertThrows(expectedType, executable)**:
    -   특정 예외가 발생하는지를 확인합니다.
    -   예를 들어, assertThrows(IllegalArgumentException.class, () -> methodThatShouldThrow())는 methodThatShouldThrow() 메서드가 IllegalArgumentException을 발생시키는지를 확인합니다.
10.  **assertDoesNotThrow(executable)**:
    -   실행 중 예외가 발생하지 않는지를 확인합니다.
    -   예를 들어, assertDoesNotThrow(() -> methodThatShouldNotThrow())는 주어진 메서드가 예외를 발생시키지 않는지 확인합니다.
11.  **assertTimeout(duration, executable)**:
    -   주어진 시간 내에 메서드가 완료되는지 확인합니다.
    -   예를 들어, assertTimeout(Duration.ofSeconds(1), () -> timeConsumingMethod())는 timeConsumingMethod()가 1초 이내에 완료되는지 확인합니다.
12.  **assertAll(executables...)**:
    -   여러 개의 assertion을 동시에 검증할 수 있습니다. 하나의 실패가 다른 assertion의 검증을 중단하지 않습니다.
    -   예를 들어, assertAll("group of assertions", () -> assertTrue(1 < 2), () -> assertEquals(4, 2 + 2))는 여러 검증을 동시에 수행합니다.

Test Fixture
테스트 픽스처는 소프트웨어 테스트에서 사용되는 용어로, 테스트 실행 전에 테스트 환경을 설정하기 위해 필요한 모든 것들을 의미합니다. 테스트 픽스처는 테스트가 실행될 때 필요한 일관된 상태를 보장하는 데 중요한 역할을 합니다. 이것은 테스트에 필요한 데이터, 객체, 설정, 파일 등 다양한 요소들을 포함할 수 있습니다.

테스트 픽스처의 목적
1. 일관성: 모든 테스트는 동일한 조건하에서 시작되어야 합니다. 픽스처는 모든 테스트에 동일한 환경을 제공합니다.
2. 재사용성: 같은 픽스처를 여러 테스트에서 재사용함으로써 코드의 중복을 줄일 수 있습니다.
3. 코드 정리: 테스트 코드에서 환경 설정 코드를 분리함으로써 테스트 코드의 가독성과 관리 용이성을 높입니다.

테스트 픽스처의 예

데이터베이스 연결 설정
테스트에 사용될 임시 파일 생성
테스트에 필요한 객체의 인스턴스화
특정 상태를 가진 Mock 객체의 생성
설정 파일 또는 환경 변수의 초기화
사용 방법
JUnit 같은 테스트 프레임워크에서는 테스트 픽스처를 구성하기 위해 다음과 같은 어노테이션을 제공합니다:

@Before/@BeforeEach: 각 테스트가 실행되기 전에 픽스처를 설정합니다.
@After/@AfterEach: 각 테스트가 실행된 후에 픽스처를 정리합니다.
@BeforeClass/@BeforeAll`: 모든 테스트가 실행되기 전에 한 번만 실행되는 픽스처를 설정합니다.
@AfterClass/@AfterAll`: 모든 테스트가 실행된 후에 한 번만 실행되는 픽스처를 정리합니다.

테스트 픽스처는 테스트의 신뢰성과 재현성을 높이는 데 중요한 역할을 하며, 테스트 코드를 더 깔끔하고 관리하기 쉽게 만들어 줍니다.

JUnit은 Java 애플리케이션을 테스트하기 위한 프레임워크로, 테스트 케이스를 작성하고 실행하는 데 사용됩니다. JUnit의 라이프 사이클은 테스트의 설정, 실행 및 정리를 관리하는 일련의 단계로 구성됩니다. 여기서는 JUnit 5를 기준으로 설명하겠습니다.

### JUnit 5 라이프 사이클

JUnit 5에서 각 테스트 클래스 및 테스트 메서드는 다음과 같은 라이프 사이클 단계를 거칩니다.

1. **@BeforeAll**: 모든 테스트 메서드가 실행되기 전에 딱 한 번 실행됩니다. 테스트 클래스가 로드될 때 한 번만 실행되며, 주로 테스트 환경의 전역 설정에 사용됩니다. 이 메서드는 반드시 `static`이어야 합니다.

2. **@BeforeEach**: 각 테스트 메서드가 실행되기 전에 실행됩니다. 각 테스트 메서드가 실행될 때마다 실행되며, 주로 테스트마다 필요한 설정에 사용됩니다.

3. **@Test**: 실제 테스트 메서드로, 테스트 논리가 포함된 메서드입니다. 각 테스트 메서드는 독립적으로 실행됩니다.

4. **@AfterEach**: 각 테스트 메서드가 실행된 후에 실행됩니다. 각 테스트 메서드가 실행될 때마다 실행되며, 주로 테스트마다 필요한 정리에 사용됩니다.

5. **@AfterAll**: 모든 테스트 메서드가 실행된 후에 딱 한 번 실행됩니다. 테스트 클래스가 언로드되기 전에 한 번만 실행되며, 주로 테스트 환경의 전역 정리에 사용됩니다. 이 메서드는 반드시 `static`이어야 합니다.

### 예제 코드

다음은 JUnit 5의 라이프 사이클을 보여주는 예제 코드입니다:

```java
import org.junit.jupiter.api.AfterAll;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

public class JUnitLifecycleTest {

    @BeforeAll
    static void beforeAll() {
        System.out.println("Before All Tests");
    }

    @BeforeEach
    void beforeEach() {
        System.out.println("Before Each Test");
    }

    @Test
    void test1() {
        System.out.println("Test 1");
    }

    @Test
    void test2() {
        System.out.println("Test 2");
    }

    @AfterEach
    void afterEach() {
        System.out.println("After Each Test");
    }

    @AfterAll
    static void afterAll() {
        System.out.println("After All Tests");
    }
}
```

### 실행 결과

위의 코드를 실행하면 다음과 같은 순서로 출력이 발생합니다:

```
Before All Tests
Before Each Test
Test 1
After Each Test
Before Each Test
Test 2
After Each Test
After All Tests
```

이 예제에서 각 어노테이션의 역할과 실행 순서를 쉽게 이해할 수 있습니다.

### 요약

- **@BeforeAll**: 테스트 클래스 전체에서 한 번 실행
- **@BeforeEach**: 각 테스트 메서드 실행 전에 실행
- **@Test**: 실제 테스트 메서드
- **@AfterEach**: 각 테스트 메서드 실행 후에 실행
- **@AfterAll**: 테스트 클래스 전체에서 한 번 실행

JUnit 라이프 사이클을 잘 이해하면 테스트 환경을 효율적으로 설정하고 정리할 수 있으며, 테스트의 독립성과 재현성을 높일 수 있습니다.