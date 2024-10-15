[원본](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-validation.html) 을 번역한 후 재정리했습니다.

### 스프링 MVC에서의 유효성 검사 (Validation)

스프링 MVC는 요청 데이터를 검증할 수 있는 유효성 검사 기능을 제공합니다. 이 기능은 입력된 데이터가 비즈니스 로직에 맞게 정확한지를 확인하는 데 매우 유용합니다. 유효성 검사는 다음 두 가지 수준에서 적용될 수 있습니다.

1. **메서드 파라미터 단위의 유효성 검사**:  
   `@ModelAttribute`, `@RequestBody`, `@RequestPart`와 같은 메서드 파라미터에 대해 `@Valid` 또는 `@Validated`를 사용하여 개별적으로 검증할 수 있습니다.
   
2. **메서드 레벨의 유효성 검사**:  
   메서드 파라미터 또는 메서드 자체에 검증 애너테이션(예: `@Min`, `@NotBlank`)을 선언하여 유효성 검사를 할 수 있습니다. 중첩된 객체까지 검증이 가능합니다.

### 주요 개념

#### 1. `@Valid`와 `@Validated`
- `@Valid` 또는 `@Validated` 애너테이션을 사용하면, 요청 데이터를 바인딩한 객체에 대해 유효성 검사를 수행합니다.
- 유효성 검사 오류가 발생하면 `Errors` 또는 `BindingResult` 객체에 오류가 저장되며, 이를 컨트롤러 메서드 내에서 처리할 수 있습니다.  
  만약 이 객체들이 없으면, 유효성 검사 실패 시 `MethodArgumentNotValidException`이 발생합니다.

#### 2. 메서드 검증
- 메서드 파라미터 또는 반환 값에 직접 유효성 검증 애너테이션을 선언하면, 스프링은 메서드 레벨에서 유효성 검사를 수행합니다.
- 유효성 검사가 실패하면 `HandlerMethodValidationException`이 발생할 수 있습니다.

#### 3. 전역 Validator 설정
- 전역적으로 커스텀 Validator를 설정하거나, 컨트롤러 단위로 `@InitBinder` 메서드를 통해 Validator를 설정할 수 있습니다.

---

### 유효성 검사 예제 코드

#### 1. `@Valid`와 `Errors`를 사용한 유효성 검사
```java
import jakarta.validation.Valid;
import org.springframework.stereotype.Controller;
import org.springframework.validation.Errors;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.PostMapping;

@Controller
public class AccountController {

    @PostMapping("/accounts")
    public String handleAccount(@Valid @ModelAttribute AccountForm form, Errors errors) {
        if (errors.hasErrors()) {
            // 유효성 검사 실패 시 처리
            return "errorPage";
        }
        // 유효성 검사 성공 시 처리
        return "successPage";
    }
}
```

- **설명**:  
  `@Valid`는 `AccountForm` 객체의 필드를 유효성 검증합니다.  
  `Errors`는 검증 실패 시 발생하는 오류 정보를 저장하며, 이를 기반으로 추가적인 오류 처리가 가능합니다.

#### 2. `@RequestBody`를 사용한 JSON 데이터 유효성 검사
```java
import jakarta.validation.Valid;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UserController {

    @PostMapping("/users")
    public ResponseEntity<String> createUser(@Valid @RequestBody User user) {
        // 유효성 검사 통과 후 처리 로직
        return ResponseEntity.ok("User created successfully!");
    }
}
```

- **설명**:  
  `@RequestBody`는 JSON 요청 데이터를 `User` 객체로 변환하고, `@Valid`를 통해 해당 객체에 유효성 검사를 수행합니다.  
  유효성 검사 실패 시 `MethodArgumentNotValidException`이 발생합니다.

#### 3. 메서드 수준의 유효성 검사
```java
import jakarta.validation.constraints.Min;
import jakarta.validation.constraints.NotBlank;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ProductController {

    @GetMapping("/product")
    public String getProduct(
        @RequestParam @NotBlank String name, 
        @RequestParam @Min(1) int quantity) {
        return "Product name: " + name + ", Quantity: " + quantity;
    }
}
```

- **설명**:  
  `@NotBlank`와 `@Min` 애너테이션을 사용해 메서드 파라미터에 대한 유효성 검사를 직접 선언합니다.  
  검증에 실패하면 `HandlerMethodValidationException`이 발생합니다.

#### 4. 전역 Validator 설정
```java
import org.springframework.context.annotation.Configuration;
import org.springframework.validation.Validator;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public Validator getValidator() {
        return new CustomValidator();  // 커스텀 Validator 설정
    }
}
```

- **설명**:  
  `WebMvcConfigurer` 인터페이스를 구현하여 전역적으로 커스텀 Validator를 설정할 수 있습니다.  
  이 Validator는 모든 컨트롤러에서 유효성 검사를 수행합니다.

#### 5. 예외 처리
유효성 검사 실패 시 발생하는 예외를 처리하는 방법 중 하나는 `@ExceptionHandler`를 사용하는 것입니다.

```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.MethodArgumentNotValidException;

@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<String> handleValidationException(MethodArgumentNotValidException ex) {
        return new ResponseEntity<>("Validation failed: " + ex.getMessage(), HttpStatus.BAD_REQUEST);
    }
}
```

- **설명**:  
  `@ControllerAdvice`는 전역적으로 예외를 처리할 수 있는 클래스입니다.  
  `@ExceptionHandler`는 특정 예외 발생 시 이를 처리하는 메서드입니다.

---

### 객체 클래스 예시

#### AccountForm 클래스
```java
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Size;

public class AccountForm {

    @NotBlank(message = "Username is required")
    @Size(min = 3, max = 20, message = "Username must be between 3 and 20 characters")
    private String username;

    @NotBlank(message = "Email is required")
    private String email;

    // 기본 생성자
    public AccountForm() {}

    // 생성자
    public AccountForm(String username, String email) {
        this.username = username;
        this.email = email;
    }

    // Getter와 Setter
    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```

#### User 클래스
```java
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Size;

public class User {

    @NotBlank(message = "Username is required")
    private String username;

    @NotBlank(message = "Password is required")
    @Size(min = 6, message = "Password must be at least 6 characters long")
    private String password;

    // 기본 생성자
    public User() {}

    // 생성자
    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

    // Getter와 Setter
    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```

---

### 요약
- `@Valid`와 `@Validated`는 요청 데이터에 대한 유효성 검사를 수행하며, 오류가 발생하면 `Errors` 또는 `BindingResult`를 통해 처리할 수 있습니다.
- 메서드 파라미터와 반환 값에 유효성 검증을 직접 선언할 수 있으며, 실패 시 `HandlerMethodValidationException`이 발생할 수 있습니다.
- 전역 Validator 설정을 통해 모든 컨트롤러에 공통 유효성 검사를 적용할 수 있으며, 예외 처리는 `@ExceptionHandler`를 사용해 처리합니다.

이 방법을 사용하면, 스프링 웹 애플리케이션에서 안전하고 정확한 데이터를 처리할 수 있습니다.