Spring Boot에서 애플리케이션이 시작될 때 특정 작업을 실행하고 싶을 때 유용하게 사용할 수 있는 `CommandLineRunner`와 `ApplicationRunner`에 대해 자세히 설명하겠습니다. 이 두 인터페이스는 모두 Spring Boot 애플리케이션이 초기화될 때 실행할 작업을 정의할 수 있는 방법을 제공합니다.

### CommandLineRunner

`CommandLineRunner`는 Spring Boot 애플리케이션이 완전히 초기화된 후 실행되는 인터페이스입니다. 이 인터페이스는 `run(String... args)` 메서드를 통해 명령줄 인수를 `String` 배열로 받아들입니다. 주로 애플리케이션 시작 시 한 번만 실행해야 하는 간단한 작업을 정의할 때 유용합니다.

#### 사용 방법

`CommandLineRunner` 인터페이스를 구현하는 클래스를 작성하고, `@Component` 어노테이션을 사용해 Spring 컨텍스트에 등록하면 됩니다. Spring Boot는 애플리케이션이 시작된 후 `run` 메서드를 호출하여 필요한 작업을 수행합니다.

```java
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class MyCommandLineRunner implements CommandLineRunner {

    @Override
    public void run(String... args) throws Exception {
        System.out.println("CommandLineRunner 실행 중입니다.");
        for (String arg : args) {
            System.out.println("Argument: " + arg);
        }
    }
}
```

#### 주요 특징
1. **단순 인수 처리**: `String... args` 형태로 단순한 문자열 배열로 인수를 받습니다.
2. **간단한 작업**: 단일 메서드로 구성되어 있어, 간단한 초기화 작업이나 로그 출력과 같은 작업에 적합합니다.
3. **명령줄 인수 사용**: 메서드에서 전달받는 인수는 애플리케이션 시작 시 추가되는 명령줄 인수를 그대로 전달받기 때문에, 간단하게 인수를 받아 작업하는 데 용이합니다.
4. **실행 순서 조정**: `@Order` 어노테이션이나 `Ordered` 인터페이스를 사용해 실행 순서를 지정할 수 있습니다. 

#### 예제
위의 `MyCommandLineRunner`에서 `args` 배열에 있는 값은 애플리케이션을 `java -jar app.jar arg1 arg2`와 같이 시작할 때의 명령줄 인수입니다. 이 경우 `arg1`과 `arg2`가 `args` 배열에 전달됩니다.

### ApplicationRunner

`ApplicationRunner`는 `CommandLineRunner`와 유사하게 애플리케이션 초기화 이후에 작업을 실행할 수 있는 인터페이스입니다. 그러나 `ApplicationRunner`는 `ApplicationArguments`라는 객체를 통해 명령줄 인수를 처리합니다. 이 객체는 명령줄 인수를 옵션 인수와 비옵션 인수로 나누어 제공합니다.

#### 사용 방법

`ApplicationRunner` 인터페이스를 구현한 클래스를 만들고 `@Component`로 등록하면 Spring Boot가 애플리케이션 초기화 후 `run` 메서드를 호출합니다.

```java
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.ApplicationArguments;
import org.springframework.stereotype.Component;

@Component
public class MyApplicationRunner implements ApplicationRunner {

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("ApplicationRunner 실행 중입니다.");
        
        // 비옵션 인수 출력
        for (String arg : args.getNonOptionArgs()) {
            System.out.println("Non-Option Argument: " + arg);
        }
        
        // 옵션 인수 확인
        if (args.containsOption("example")) {
            System.out.println("example 옵션이 포함되어 있습니다.");
        }
    }
}
```

#### 주요 특징
1. **ApplicationArguments 사용**: `ApplicationArguments`는 `CommandLineRunner`의 `String... args`와는 다르게 명령줄 인수를 옵션과 비옵션으로 구분합니다.
   - **비옵션 인수**: `getNonOptionArgs()`로 호출할 수 있으며, 일반적인 명령줄 인수(`java -jar app.jar arg1 arg2`에서 `arg1`, `arg2`)가 포함됩니다.
   - **옵션 인수**: `--key=value` 형태의 옵션 인수를 포함하며, `containsOption("key")`로 옵션의 존재 여부를 확인하거나, `getOptionValues("key")`로 값을 가져올 수 있습니다.
2. **옵션 인수에 유용**: `ApplicationArguments`를 사용하면 `--key=value`와 같은 형식의 옵션 인수를 보다 쉽게 처리할 수 있어, 명령줄 인수가 많은 경우 유리합니다.
3. **실행 순서 조정 가능**: `CommandLineRunner`와 마찬가지로 `@Order` 어노테이션이나 `Ordered` 인터페이스를 통해 실행 순서를 지정할 수 있습니다.

#### 예제
위의 `MyApplicationRunner`에서 `ApplicationArguments` 객체를 사용해 `example` 옵션이 포함된 경우 메시지를 출력하도록 했습니다. 예를 들어 애플리케이션을 `java -jar app.jar arg1 --example=test`로 실행할 때 `arg1`은 비옵션 인수, `--example=test`는 옵션 인수로 분류됩니다.

### CommandLineRunner와 ApplicationRunner의 차이점 요약

| 특징                | CommandLineRunner                             | ApplicationRunner                                      |
|--------------------|----------------------------------------------|-------------------------------------------------------|
| 인수 처리 방식        | `String... args`                            | `ApplicationArguments args`                           |
| 명령줄 인수 구분      | 단순 문자열 배열 처리                        | 옵션 인수와 비옵션 인수 구분 가능                     |
| 주요 용도            | 간단한 초기화 작업, 로그 출력 등              | 옵션 인수가 많은 경우, 세밀한 명령줄 인수 처리에 적합  |
| 실행 순서 지정       | `@Order` 어노테이션, `Ordered` 인터페이스     | `@Order` 어노테이션, `Ordered` 인터페이스             |

### 사용 사례

- **CommandLineRunner**: 단순한 명령줄 인수나 복잡하지 않은 초기화 작업이 필요할 때 사용하기 적합합니다.
- **ApplicationRunner**: 애플리케이션 초기화 시 옵션 인수를 세밀하게 처리해야 하는 경우, 예를 들어, 다양한 옵션을 통해 초기화 파라미터를 조정해야 하는 작업에 적합합니다.

두 인터페이스 모두 애플리케이션이 시작될 때 필요한 작업을 자동으로 수행할 수 있도록 하는 편리한 방법을 제공하므로, 필요한 작업과 인수의 복잡도에 따라 적절한 인터페이스를 선택하면 됩니다.