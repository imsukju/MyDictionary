Mockito는 자바에서 테스트를 위해 사용되는 매우 유용한 도구입니다. 이 도구를 사용하면 실제 객체를 생성하지 않고도 가짜(Mock) 객체를 만들어서 테스트할 수 있습니다. 가짜 객체를 이용하면 테스트하고자 하는 코드가 다른 클래스에 의존하지 않도록 할 수 있습니

### 1. 프로젝트에 Mockito 설정하기

먼저, 프로젝트에서 Mockito를 사용하려면 해당 라이브러리를 추가해야 합니다. Maven이나 Gradle 같은 빌드 도구를 사용하여 프로젝트에 쉽게 추가할 수 있습니다. 예를 들어 Maven을 사용하는 경우, `pom.xml` 파일에 다음과 같이 추가하면 됩니다:

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>5.2.0</version>
    <scope>test</scope>
</dependency>
```

### 2. Mock 객체 생성하기

Mock 객체는 실제 객체처럼 행동하지만, 테스트 환경에서만 사용되는 가짜 객체입니다. 두 가지 방법으로 Mock 객체를 생성할 수 있습니다:

- **@Mock 어노테이션 사용**: 테스트 클래스 내에서 `@Mock` 어노테이션을 사용하여 간편하게 Mock 객체를 선언할 수 있습니다.

    ```java
    import org.mockito.Mock;
    import org.mockito.MockitoAnnotations;

    public class UserServiceTest {
        @Mock
        private UserDao userDao;

        public void setUp() {
            MockitoAnnotations.openMocks(this);
        }
    }
    ```

    여기서 `setUp()` 메서드는 테스트를 시작하기 전에 Mock 객체를 초기화하는 역할을 합니다.

- **Mockito.mock() 메서드 사용**: 직접 `Mockito.mock()` 메서드를 사용해서 Mock 객체를 생성할 수도 있습니다.

    ```java
    UserDao userDao = Mockito.mock(UserDao.class);
    ```

### 3. Mock 객체의 행동 설정하기

Mock 객체가 특정 메서드를 호출했을 때 어떤 행동을 할지 설정할 수 있습니다. 예를 들어, `when().thenReturn()`을 사용하여 메서드가 호출되면 특정 값을 반환하도록 할 수 있습니다:

```java
import static org.mockito.Mockito.when;

when(userDao.getUserById(1)).thenReturn(new User("John"));
```

이 코드는 `userDao.getUserById(1)` 메서드가 호출될 때, `"John"`이라는 이름을 가진 `User` 객체를 반환하도록 설정한 것입니다.

### 4. Mock 객체의 동작 검증하기

테스트에서 중요한 부분은 Mock 객체가 예상대로 동작했는지를 확인하는 것입니다. 이를 위해 `verify()` 메서드를 사용합니다:

```java
import static org.mockito.Mockito.verify;

verify(userDao).getUserById(1);
```

이 코드는 `userDao.getUserById(1)` 메서드가 호출되었는지 확인하는 것입니다.

또한 메서드가 특정 횟수만큼 호출되었는지 검증할 수 있습니다:

```java
import static org.mockito.Mockito.times;

verify(userDao, times(1)).getUserById(1);
```

이 코드는 메서드가 한 번 호출되었는지를 확인합니다.

### 5. 실제 객체와 Mock 객체를 함께 사용하는 Spy

Spy는 실제 객체를 감싸서 사용하는 Mock의 일종입니다. 예를 들어, 실제 리스트 객체를 감싸는 Spy 객체를 만들 수 있습니다:

```java
List<String> list = new ArrayList<>();
List<String> spyList = Mockito.spy(list);
```

Spy 객체는 실제 객체의 일부 메서드만 Mocking할 수 있습니다:

```java
Mockito.doReturn("first element").when(spyList).get(0);
```

이 코드는 `spyList.get(0)`이 호출될 때 `"first element"`를 반환하도록 합니다.

### 6. Mock 객체의 상태 리셋하기

테스트 중에 Mock 객체의 상태를 초기화해야 할 때 `Mockito.reset()`을 사용할 수 있습니다:

```java
Mockito.reset(userDao);
```

### 실습 예제: UserService 테스트하기

UserService라는 클래스를 테스트한다고 가정해 봅시다. 이 클래스는 `UserDao`와 `EmailService`라는 두 개의 의존 객체에 의존하고 있습니다. 우리의 목표는 `UserService`가 이 두 의존성을 제대로 호출하는지 테스트하는 것입니다.

먼저, `UserServiceTest`라는 테스트 클래스를 만들고, Mock 객체들을 생성하여 주입합니다:

```java
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

public class UserServiceTest {
    @Mock
    private UserDao userDao;

    @Mock
    private EmailService emailService;

    @InjectMocks
    private UserService userService;

    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    // 테스트 메서드들...
}
```

이제 UserService의 `addUser` 메서드를 테스트해 봅시다:

```java
import static org.mockito.Mockito.when;
import static org.mockito.Mockito.verify;

public class UserServiceTest {

    public void testAddUser() {
        // Mock 객체의 행동 설정
        when(userDao.addUser(any(User.class))).thenReturn(true);

        // EmailService 동작 설정
        doNothing().when(emailService).sendWelcomeEmail(any(User.class));

        // 테스트 대상 메서드 호출
        User user = new User("John", "john@example.com");
        boolean result = userService.addUser(user);

        // 동작 검증
        verify(userDao).addUser(user);
        verify(emailService).sendWelcomeEmail(user);

        // 결과 검증
        assertTrue(result);
    }
}
```

이 코드에서 `addUser` 메서드가 실행될 때, `UserDao`와 `EmailService`가 올바르게 호출되었는지 검증합니다.

### 결론

Mockito는 테스트에서 실제 객체를 사용하지 않고도 코드의 동작을 검증할 수 있는 강력한 도구입니다. Mock 객체를 사용하면 외부 의존성 없이도 테스트를 독립적으로 수행할 수 있어, 보다 견고하고 유지보수하기 쉬운 테스트 코드를 작성할 수 있습니다.