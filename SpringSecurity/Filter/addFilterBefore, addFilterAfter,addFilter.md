Spring Security에서 `addFilterBefore`, `addFilterAfter`, 그리고 `addFilter`는 `SecurityFilterChain`에 커스텀 필터를 추가할 때 사용하는 메서드들입니다. 이들은 필터의 위치를 지정하여 **필터 체인의 실행 순서를 조정**하는 데 중요한 역할을 합니다. 각 메서드의 차이점과 구체적인 작동 방식을 상세히 설명드리겠습니다.

---

### 1. `addFilterBefore`

- **설명**: `addFilterBefore`는 지정한 기존 필터 **앞에** 커스텀 필터를 추가합니다.  
- **예시**: `http.addFilterBefore(new CustomFilter(), UsernamePasswordAuthenticationFilter.class);`
  - 이 코드의 경우, `CustomFilter`가 `UsernamePasswordAuthenticationFilter` 앞에 추가되어 **해당 필터보다 먼저 실행**됩니다.
- **사용 목적**: 이 메서드는 주로 **인증 로직을 선행 처리해야 하는 경우**에 유용합니다. 예를 들어, API 요청에서 특정 헤더나 토큰의 유효성을 먼저 검사하거나, 권한에 따라 접근을 제한할 때 사용됩니다.
- **예시 상황**: JWT 토큰을 먼저 확인하고, 유효할 때만 기본 인증을 수행하도록 하려면, `JwtAuthenticationFilter`를 `UsernamePasswordAuthenticationFilter`보다 먼저 실행시키면 됩니다.

---

### 2. `addFilterAfter`

- **설명**: `addFilterAfter`는 지정한 기존 필터 **뒤에** 커스텀 필터를 추가합니다.
- **예시**: `http.addFilterAfter(new CustomFilter(), UsernamePasswordAuthenticationFilter.class);`
  - 이 코드의 경우, `CustomFilter`는 `UsernamePasswordAuthenticationFilter`가 실행된 **후에 실행**됩니다.
- **사용 목적**: **기존 인증이 끝난 후 추가적인 검증이나 후속 처리가 필요한 경우**에 사용됩니다. 예를 들어, 이미 인증이 완료된 후 권한을 확인하는 작업이나, 인증된 사용자 정보를 추가적으로 로깅하거나 필터링해야 할 때 유용합니다.
- **예시 상황**: 모든 요청을 로깅하는 필터가 있다고 가정하면, 인증이 완료된 이후 로깅 필터를 실행해 인증된 사용자 정보를 로깅하는 데 적합합니다.

---

### 3. `addFilter`

- **설명**: `addFilter`는 커스텀 필터를 **체인의 마지막 위치**에 추가합니다. 이 메서드는 순서를 명시하지 않으며, 기본적으로 **체인에서 가장 뒤에 위치**시킵니다.
- **예시**: `http.addFilter(new CustomFilter());`
  - `CustomFilter`가 Security Filter Chain의 마지막에 추가됩니다.
- **사용 목적**: `addFilter`는 필터의 순서가 크게 중요하지 않거나, **마지막에 후처리가 필요할 때** 적합합니다. 주로 특정 필터의 위치가 기존 필터 순서에 의존하지 않을 경우에 사용됩니다.
- **예시 상황**: 요청/응답 로깅과 같이 요청의 앞부분에서 처리할 필요가 없고, 모든 인증 및 권한 검사가 끝난 후에 작동해도 무방한 경우에 사용됩니다.

---

### 추가적인 실행 순서에 관한 예시

`SecurityFilterChain` 내의 기본 필터들은 특정 순서로 실행됩니다. 일반적으로는 `addFilterBefore`나 `addFilterAfter`를 통해 **커스텀 필터가 기본 필터들 사이에서 특정 위치**에 자리하도록 설정합니다.

#### 예시 코드로 실행 순서 확인

다음 코드로 커스텀 필터를 `SecurityFilterChain` 내의 다양한 위치에 추가한 예를 볼 수 있습니다.

```java
http
    .addFilterBefore(new CustomFilterBefore(), UsernamePasswordAuthenticationFilter.class)   // 가장 앞
    .addFilterAfter(new CustomFilterAfter(), BasicAuthenticationFilter.class)               // 중간 (인증 완료 후)
    .addFilter(new CustomFilterEnd());                                                      // 가장 뒤
```

### 요약 표

| 메서드            | 추가 위치                              | 주요 용도                            |
|-------------------|---------------------------------------|--------------------------------------|
| `addFilterBefore` | 지정한 필터 **앞에** 추가              | 인증 로직 선처리, JWT 토큰 검사      |
| `addFilterAfter`  | 지정한 필터 **뒤에** 추가              | 인증 후 처리, 추가적인 로깅/검증     |
| `addFilter`       | 체인의 **마지막**에 추가               | 순서가 중요하지 않은 후처리, 로깅 등 |

필터 추가 시, 필터가 어느 단계에서 작동해야 하는지를 정확히 파악해 `addFilterBefore`와 `addFilterAfter`를 선택하는 것이 Spring Security의 인증 흐름에 맞춘 필터링을 구현하는 데 중요합니다.