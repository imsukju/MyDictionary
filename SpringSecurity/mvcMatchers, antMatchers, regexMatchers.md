Spring Security에서 URL 접근을 제어하기 위해 사용되는 `mvcMatchers`, `antMatchers`, `regexMatchers`에 대해 자세히 설명드리겠습니다. 이 중 일부는 Spring Boot 2.7 및 Spring Security 5.7 이후로 사용 방식이 변경되었으니, 이에 대해서도 설명드리겠습니다.

### 1. `mvcMatchers`
- **설명**: `mvcMatchers`는 Spring MVC의 URL 매핑 방식을 따르는 매처입니다. 이 방식은 Spring MVC에서 사용하는 URL 패턴 매칭 규칙을 그대로 사용합니다.
- **특징**:
  - 지역화된 경로 매칭을 지원합니다.
  - Spring MVC의 `PathVariable`이나 `RequestParam` 등의 매개변수 패턴을 다룰 때 유리합니다.
  - 경로가 슬래시(`/`)로 끝나면 그에 대한 경로 일치를 강제합니다.
- **사용 예시**:
  ```java
  http.authorizeRequests()
      .mvcMatchers("/admin/**").hasRole("ADMIN")
      .mvcMatchers("/user/{id}").hasAnyRole("USER", "ADMIN");
  ```
- **지원 변화**: Spring Boot 3.0 이상 및 Spring Security 5.7 이상에서는 권장되지 않는 방식입니다. 대신 `authorizeHttpRequests`와 같은 메서드와 함께 새로운 방식의 매칭을 사용하도록 권장합니다.

### 2. `antMatchers`
- **설명**: `antMatchers`는 Apache Ant 스타일의 패턴을 사용해 URL 경로를 매칭하는 방식입니다. 와일드카드(`*` 및 `**`)를 사용해 URL 패턴을 지정할 수 있습니다.
- **특징**:
  - `*`는 한 단계 경로 매칭, `**`는 모든 경로 매칭을 의미합니다.
  - 경로의 끝에 슬래시(`/`)가 있어도, 없어도 매칭에 큰 영향을 미치지 않습니다.
- **사용 예시**:
  ```java
  http.authorizeRequests()
      .antMatchers("/public/**").permitAll()
      .antMatchers("/admin/**").hasRole("ADMIN");
  ```
- **지원 변화**: `mvcMatchers`와 마찬가지로, Spring Boot 3.0 및 Spring Security 5.7 이후부터는 더 이상 권장되지 않으며, `authorizeHttpRequests`와 같은 메서드로 교체하는 것이 권장됩니다.

### 3. `regexMatchers`
- **설명**: `regexMatchers`는 정규 표현식을 사용해 경로를 매칭하는 방식입니다. 복잡한 패턴이나 조건을 사용하는 URL 매칭에 유리합니다.
- **특징**:
  - Java의 정규 표현식 문법을 사용해 다양한 조건 매칭이 가능합니다.
  - 예를 들어, 특정 파일 확장자나 특정 숫자 패턴에 대한 URL을 매칭할 수 있습니다.
- **사용 예시**:
  ```java
  http.authorizeRequests()
      .regexMatchers("^/admin/.*").hasRole("ADMIN")
      .regexMatchers(".*/(jpg|png)$").permitAll();
  ```
- **지원 변화**: Spring Security 5.7 이후부터는 `regexMatchers`의 사용이 권장되지 않으며, 새로운 방식으로 매칭을 구현하는 것이 좋습니다.

### Spring Security 5.7 / Spring Boot 3.0 이후의 변경 사항
Spring Security 5.7 이후부터는 `authorizeRequests()` 대신 **`authorizeHttpRequests()`**를 사용하도록 권장됩니다. 또한 `mvcMatchers`, `antMatchers`, `regexMatchers` 대신에 **`requestMatchers()`** 메서드를 사용하여 패턴 매칭을 하나로 통합하는 방식으로 변경되었습니다. 예를 들어:

```java
http.authorizeHttpRequests()
    .requestMatchers("/admin/**").hasRole("ADMIN")
    .requestMatchers("/user/**").authenticated()
    .anyRequest().permitAll();
```

### 요약
- **Spring Boot 3.0 / Spring Security 5.7 이후 권장 방식**: `authorizeHttpRequests()` + `requestMatchers()`
- `mvcMatchers`, `antMatchers`, `regexMatchers`는 더 이상 권장되지 않으며 향후 지원이 중단될 가능성이 있습니다.