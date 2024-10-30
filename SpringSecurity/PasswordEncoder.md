### 비밀번호 저장

Spring Security의 `PasswordEncoder` 인터페이스는 비밀번호를 안전하게 저장하기 위해 비밀번호를 단방향 변환하는 데 사용됩니다. `PasswordEncoder`는 단방향 변환이기 때문에 데이터베이스 인증 등에 필요한 양방향 변환이 필요할 때는 적합하지 않습니다. 보통 `PasswordEncoder`는 사용자가 인증할 때 제공한 비밀번호를 비교하기 위해 저장된 비밀번호를 암호화하는 데 사용됩니다.

### 비밀번호 저장의 역사

초기에는 비밀번호를 평문으로 저장하는 것이 표준 방식이었습니다. 비밀번호를 저장하는 데이터 저장소에 접근하려면 자격 증명이 필요하다는 점에서 비밀번호는 안전하다고 여겨졌습니다. 그러나 SQL 인젝션과 같은 공격을 통해 대량의 사용자 이름과 비밀번호가 포함된 "데이터 덤프"를 얻을 수 있는 방법이 밝혀지면서 보안 전문가들은 비밀번호를 더욱 안전하게 보호해야 한다는 것을 깨달았습니다.

이후 개발자들은 SHA-256과 같은 단방향 해시를 사용하여 비밀번호를 저장하는 것이 권장되었습니다. 사용자가 인증을 시도할 때, 저장된 해시된 비밀번호를 사용자가 입력한 비밀번호의 해시 값과 비교하는 방식입니다. 이렇게 하면 시스템에는 비밀번호의 단방향 해시만 저장되므로, 만약 데이터가 유출되더라도 해시 값만 노출됩니다. 해시 값은 단방향이며 복구가 어려워 해시 값을 이용해 비밀번호를 추측하는 데 많은 비용이 듭니다. 하지만 악의적인 사용자는 이를 무력화하기 위해 레인보우 테이블이라고 불리는 조회 테이블을 만들었습니다. 레인보우 테이블은 모든 비밀번호의 해시 값을 미리 계산하여 저장해두는 방식입니다.

레인보우 테이블을 방지하기 위해 개발자들은 솔트(salt)를 사용한 비밀번호 저장을 권장받았습니다. 솔트는 각 사용자의 비밀번호마다 생성되는 무작위 바이트 데이터로, 비밀번호와 함께 해시 함수에 입력되어 고유한 해시 값을 생성합니다. 솔트는 평문으로 비밀번호와 함께 저장됩니다. 이후 사용자가 인증을 시도할 때, 저장된 솔트와 비밀번호의 해시 값을 비교합니다. 솔트 덕분에 레인보우 테이블을 사용하는 공격은 무력화되며, 해시 값은 각 솔트와 비밀번호 조합마다 다릅니다.

현대에는 암호화 해시(SHA-256 등)가 더 이상 안전하지 않다는 것이 밝혀졌습니다. 현대 하드웨어는 초당 수십억 개의 해시 계산을 수행할 수 있으므로 개별 비밀번호를 쉽게 추측할 수 있습니다.

현재 개발자들은 비밀번호를 저장할 때 적응형 단방향 함수(adaptive one-way functions)를 사용하는 것이 권장됩니다. 적응형 단방향 함수는 의도적으로 많은 CPU, 메모리, 기타 자원을 사용하여 비밀번호 검증을 자원 집약적으로 만듭니다. 이러한 함수는 하드웨어 성능이 향상될수록 검증에 소요되는 "작업량(work factor)"을 증가시킬 수 있습니다. 일반적으로 시스템에서 비밀번호 검증에 약 1초가 걸리도록 작업량을 조정하는 것이 좋습니다. 이렇게 하면 공격자가 비밀번호를 해독하기 어렵지만, 시스템 자체에 과도한 부담을 주지 않고 사용자에게도 불편을 주지 않는 균형을 유지할 수 있습니다. Spring Security는 작업량을 적절하게 설정할 수 있는 기본값을 제공하지만, 사용자가 직접 시스템 성능에 맞춰 작업량을 조정하는 것이 권장됩니다. bcrypt, PBKDF2, scrypt, argon2와 같은 적응형 단방향 함수가 좋은 예입니다.

적응형 단방향 함수는 자원 집약적이기 때문에 요청마다 사용자 이름과 비밀번호를 검증하면 애플리케이션 성능이 크게 저하될 수 있습니다. Spring Security 또는 다른 라이브러리들은 비밀번호 검증 속도를 빠르게 할 수 없으므로, 사용자는 장기 자격 증명(사용자 이름과 비밀번호)을 단기 자격 증명(세션 또는 OAuth 토큰 등)으로 교환하는 것이 권장됩니다. 단기 자격 증명은 빠르게 검증할 수 있으며, 보안성은 유지됩니다.

---

### 비밀번호 인코딩 (Password Encoding)

비밀번호를 인코딩할 때, `idForEncode`는 비밀번호를 변환할 암호화 알고리즘(즉, 어떤 `PasswordEncoder`를 사용할지)을 결정하는 중요한 역할을 합니다. 이 값은 생성자에서 전달되어 `DelegatingPasswordEncoder`의 인코딩 작업이 어떤 방식으로 처리될지 지정합니다.

예를 들어, `idForEncode`가 "bcrypt"로 설정되면, 인코딩된 결과는 `BCryptPasswordEncoder`에 의해 처리되며, 최종적으로 인코딩된 비밀번호는 `{bcrypt}`라는 접두사로 시작합니다. 이 접두사는 어떤 알고리즘이 사용되었는지를 나타내므로, 비밀번호를 저장하거나 인증할 때 적절한 암호화 방식으로 해석할 수 있게 도와줍니다.

예시:
```plaintext
{bcrypt}$2a$10$dXJ3SW6G7P50lGmMkkmwe.20cQQubK3.HZWzG3YB1tlRy.fqvM/BG
```

이 예시에서, `{bcrypt}`는 `BCryptPasswordEncoder`가 사용되었음을 명시하고, 뒤에 따라오는 문자열은 인코딩된 비밀번호 값입니다.

### 비밀번호 매칭 (Password Matching)

비밀번호 검증 과정은 비밀번호 앞에 붙어 있는 `{id}`에 따라 적절한 `PasswordEncoder`를 매핑하여 수행됩니다. 이 매핑은 생성자에서 제공된 id 값에 기반하며, 예를 들어 `{bcrypt}`가 붙어 있으면 `BCryptPasswordEncoder`가 사용됩니다.

비밀번호를 비교하는 과정에서, `matches(CharSequence rawPassword, String encodedPassword)` 메서드가 사용됩니다. 이 메서드는 사용자가 입력한 평문 비밀번호(`rawPassword`)를 인코딩된 비밀번호(`encodedPassword`)와 비교합니다. 만약 비밀번호의 id가 생성자에 매핑되지 않거나 null인 경우, `IllegalArgumentException`이 발생합니다.

이 동작은 `DelegatingPasswordEncoder.setDefaultPasswordEncoderForMatches(PasswordEncoder)` 메서드를 사용하여 기본 비밀번호 인코더를 지정해 커스터마이징할 수 있습니다.

이 방식은 최신 인코딩 방식으로 비밀번호를 저장하면서도, 시스템에 이미 저장된 비밀번호를 이전 방식으로 계속 매칭할 수 있도록 해 줍니다. 특히 비밀번호 해시는 암호화와 달리 평문을 복구할 수 없도록 설계되어 있어, 해시된 비밀번호를 마이그레이션하기 어렵습니다. 예를 들어 `NoOpPasswordEncoder`를 사용해 비밀번호를 마이그레이션하는 것은 간단하지만, 보안성을 유지하기 위해 최신 방식의 암호화를 사용하는 것이 좋습니다.

### DelegatingPasswordEncoder

Spring Security 5.0 이전에는 기본 `PasswordEncoder`가 `NoOpPasswordEncoder`였으며, 이는 평문으로 비밀번호를 저장하는 방식이었습니다. 하지만 보안 문제로 인해 Spring Security는 `DelegatingPasswordEncoder`를 도입하여 다음과 같은 문제를 해결했습니다:

1. 많은 애플리케이션에서 이전 방식의 비밀번호 인코딩을 사용하고 있으며, 이를 쉽게 마이그레이션하기 어렵습니다.
2. 비밀번호 저장의 최선의 방식은 시간이 지나면서 계속 변화할 수 있습니다.
3. 프레임워크가 빈번하게 변경될 수 없으므로, 안정성을 유지해야 합니다.

`DelegatingPasswordEncoder`는 이 세 가지 문제를 해결하기 위해 다음과 같은 기능을 제공합니다:

- 현재 비밀번호 저장 권장 방식으로 비밀번호를 인코딩합니다.
- 과거 또는 최신 방식으로 비밀번호를 검증할 수 있습니다.
- 향후 새로운 암호화 알고리즘이 도입되었을 때 쉽게 업그레이드할 수 있습니다.

`DelegatingPasswordEncoder`는 `PasswordEncoderFactories.createDelegatingPasswordEncoder()` 메서드를 통해 기본적으로 생성할 수 있습니다. 이 메서드는 기본적으로 최신 암호화 알고리즘을 사용하는 `PasswordEncoder`를 반환합니다.

```java
PasswordEncoder passwordEncoder = PasswordEncoderFactories.createDelegatingPasswordEncoder();
```

또한, 필요에 따라 사용자 정의 암호화 인코더를 설정할 수도 있습니다. 예를 들어, 여러 암호화 알고리즘을 사용하고 싶다면 `DelegatingPasswordEncoder`를 사용자 정의 방식으로 설정할 수 있습니다. 다음은 여러 인코딩 알고리즘을 매핑한 사용자 정의 `DelegatingPasswordEncoder` 예시입니다:

```java
String idForEncode = "bcrypt";  // 기본 인코딩 방식으로 bcrypt 사용
Map<String, PasswordEncoder> encoders = new HashMap<>();
encoders.put(idForEncode, new BCryptPasswordEncoder());  // bcrypt로 비밀번호 인코딩
encoders.put("noop", NoOpPasswordEncoder.getInstance());  // 평문 저장
encoders.put("pbkdf2", Pbkdf2PasswordEncoder.defaultsForSpringSecurity_v5_5());  // PBKDF2 인코딩
encoders.put("pbkdf2@SpringSecurity_v5_8", Pbkdf2PasswordEncoder.defaultsForSpringSecurity_v5_8());
encoders.put("scrypt", SCryptPasswordEncoder.defaultsForSpringSecurity_v4_1());  // Scrypt 인코딩
encoders.put("scrypt@SpringSecurity_v5_8", SCryptPasswordEncoder.defaultsForSpringSecurity_v5_8());
encoders.put("argon2", Argon2PasswordEncoder.defaultsForSpringSecurity_v5_2());  // Argon2 인코딩
encoders.put("argon2@SpringSecurity_v5_8", Argon2PasswordEncoder.defaultsForSpringSecurity_v5_8());
encoders.put("sha256", new StandardPasswordEncoder());  // SHA-256 인코딩 (권장되지 않음)

PasswordEncoder passwordEncoder = new DelegatingPasswordEncoder(idForEncode, encoders);  // 사용자 정의 인코더 설정
```

위의 코드를 보면 다양한 암호화 방식이 정의되어 있으며, 그 중 기본적으로는 bcrypt를 사용하도록 설정했습니다. 또한 필요에 따라 기존의 인코딩 방식(`noop`, `pbkdf2`, `scrypt`, `argon2`, `sha256`)도 지원할 수 있습니다.

이러한 방식으로 설정된 `DelegatingPasswordEncoder`는 다양한 비밀번호 인코딩 및 매칭을 처리할 수 있으며, 향후 새로운 암호화 방식이 도입되더라도 손쉽게 업그레이드할 수 있습니다.

---


### 비밀번호 저장 형식

비밀번호의 일반적인 형식은 다음과 같습니다:

```plaintext
{id}encodedPassword
```

`id`는 사용할 `PasswordEncoder`를 조회하는 데 사용되는 식별자이며, `encodedPassword`는 선택한 `PasswordEncoder`에 의해 인코딩된 비밀번호입니다. `id`는 비밀번호의 시작 부분에 있어야 하며, `{`로 시작하고 `}`로 끝나야 합니다.

이처럼 여러 가지 비밀번호 인코딩 방식을 사용하여 비밀번호를 안전하게 관리할 수 있습니다. `DelegatingPasswordEncoder`를 사용하면 현대적이거나 레거시 형식으로 비밀번호를 처리하면서도 향후 더 안전한 방식으로 비밀번호 인코딩 방식을 업그레이드할 수 있습니다.