### Spring Security의 Authentication(인증)과 Authorization(권한 부여)

**Spring Security**는 웹 애플리케이션의 보안을 처리하는 강력한 프레임워크입니다. 웹사이트를 구축하다 보면, 사용자의 로그인 과정과 권한 관리는 필수적으로 다뤄야 하는 주제입니다. 이 글에서는 Spring Security의 핵심 개념인 **Authentication(인증)**과 **Authorization(권한 부여)**를 기초부터 자세히 설명하겠습니다. 이를 통해 인증과 권한 부여의 차이와, 어떻게 수동으로 처리할 수 있는지에 대해서도 알아보겠습니다.

---

### Authentication(인증)이란 무엇인가?

**Authentication(인증)**은 "이 사용자가 정말 누구인가?"를 확인하는 과정입니다. 예를 들어, 우리가 웹사이트에 로그인할 때, 사용자는 보통 사용자 이름과 비밀번호를 입력하게 됩니다. 이때 입력된 정보가 실제로 올바른지 확인하는 과정을 **인증**이라고 부릅니다.

Spring Security는 사용자가 올바른 자격 증명(예: 사용자 이름, 비밀번호)을 제공했는지 확인하고, 인증이 성공하면 해당 사용자 정보를 `Authentication` 객체에 담아 **인증된 상태**로 유지합니다.

#### Authentication의 흐름

1. **인증 요청 (Authentication Request)**: 사용자가 로그인할 때 자격 증명(사용자 이름과 비밀번호)을 입력합니다.
2. **인증 처리**: Spring Security는 입력된 정보를 기반으로 사용자를 인증합니다.
3. **Authentication 객체 생성**: 인증이 성공하면, 사용자 정보를 포함한 `Authentication` 객체가 생성되고, 인증된 상태가 됩니다.

이 `Authentication` 객체는 보통 Spring Security가 관리하는 **SecurityContext**에 저장되며, 이를 통해 애플리케이션은 사용자가 인증된 상태인지 확인할 수 있습니다.

#### Authentication 객체의 두 가지 상태

1. **인증 전 (authenticated=false)**: 사용자가 아직 인증되지 않은 상태입니다. 사용자가 시스템에 접근하려면, 이 인증 절차를 통과해야 합니다.
2. **인증 후 (authenticated=true)**: 사용자가 자격 증명을 올바르게 제공하여 인증이 완료된 상태입니다. 이제 사용자는 인증된 사용자로서 애플리케이션의 리소스에 접근할 수 있습니다.

---

### SecurityContext와 SecurityContextHolder

**SecurityContext**는 Spring Security에서 인증된 사용자 정보를 유지하는 공간입니다. 한 번 인증이 완료되면, 이 정보를 애플리케이션 내에서 재사용할 수 있도록 `SecurityContext`에 저장합니다. 이를 통해 사용자마다 고유한 인증 정보를 유지할 수 있습니다.

- **SecurityContextHolder**: `SecurityContextHolder`는 `SecurityContext`를 관리하는 역할을 합니다. 보통 **스레드 로컬(ThreadLocal)**을 사용해 각 사용자의 인증 정보를 독립적으로 관리합니다. 즉, 하나의 스레드에서 실행되는 모든 코드는 특정 사용자의 `SecurityContext`에 접근할 수 있습니다.

---

### Authorization(권한 부여)란 무엇인가?

**Authorization(권한 부여)**는 "이 사용자가 무엇을 할 수 있는가?"를 결정하는 과정입니다. 인증(Authentication)을 통해 사용자가 시스템에 로그인한 후, 이제는 그 사용자가 애플리케이션 내에서 **어떤 권한을 가지는지** 결정해야 합니다.

예를 들어, 관리자(admin) 권한을 가진 사용자는 중요한 데이터를 편집할 수 있지만, 일반 사용자(user)는 그 데이터에 대한 읽기 권한만 가질 수 있습니다. 이처럼 권한 부여는 인증된 사용자가 시스템 내에서 할 수 있는 작업을 제어하는 중요한 역할을 합니다.

#### Authorization의 흐름

1. **인증 후 권한 확인**: 사용자가 인증된 후, 그 사용자가 시스템 내에서 어떤 자원에 접근할 수 있는지 확인합니다.
2. **권한 검사**: 사용자가 요청한 리소스에 접근할 수 있는 권한이 있는지 검사합니다. 예를 들어, 관리자만 접근할 수 있는 페이지에 일반 사용자가 접근하려 한다면, 접근이 거부됩니다.

#### 권한 부여에서 사용되는 개념: `GrantedAuthority`

Spring Security는 사용자에게 부여된 권한을 `GrantedAuthority` 객체로 관리합니다. 각 사용자는 여러 권한을 가질 수 있으며, 애플리케이션은 이 권한 정보를 기반으로 사용자가 어떤 작업을 수행할 수 있는지 결정합니다.

---

### Authentication(인증)과 Authorization(권한 부여)의 차이점

1. **Authentication(인증)**: "이 사용자가 누구인가?"를 확인하는 과정입니다. 로그인 시도 시 자격 증명을 확인하여 사용자를 식별합니다.
2. **Authorization(권한 부여)**: "이 사용자가 무엇을 할 수 있는가?"를 결정하는 과정입니다. 사용자가 인증된 후, 어떤 리소스에 접근할 수 있는지, 어떤 작업을 수행할 수 있는지를 제어합니다.

#### 인증과 권한 부여의 실제 예시

1. **Authentication (인증)**
   - 사용자가 로그인 페이지에서 사용자 이름과 비밀번호를 입력합니다.
   - Spring Security는 이를 확인하고, 올바른 자격 증명을 제공한 경우 `Authentication` 객체를 생성하여 사용자를 인증합니다.

2. **Authorization (권한 부여)**
   - 사용자가 인증된 후, 애플리케이션 내의 특정 페이지나 리소스에 접근하려고 합니다.
   - 시스템은 그 사용자의 권한을 확인하고, 해당 자원에 접근할 수 있는지 판단합니다. 만약 권한이 없다면 접근이 차단됩니다.

---

### 직접 인증 객체 설정하기

대부분의 경우 Spring Security는 로그인 과정에서 자동으로 인증과 권한 부여를 처리합니다. 하지만, 필요에 따라 수동으로 `Authentication` 객체를 설정해야 할 때도 있습니다. 예를 들어, 커스텀 인증 로직을 구현하거나 외부 시스템과 통합할 때입니다.

다음은 직접 인증 객체를 설정하는 예시입니다:

```java
SecurityContext context = SecurityContextHolder.createEmptyContext();
context.setAuthentication(anAuthentication);  // 수동으로 인증 정보 설정
SecurityContextHolder.setContext(context);     // SecurityContext에 저장
```

이 코드는 현재 스레드에 빈 `SecurityContext`를 생성하고, 사용자가 인증된 것처럼 `Authentication` 객체를 설정한 후, 그 컨텍스트를 `SecurityContextHolder`에 저장하는 과정입니다.

#### 주의할 점

`Authentication` 객체를 수동으로 설정할 때는, 해당 객체의 `authenticated` 속성이 `true`로 설정되지 않는다면, 여전히 시스템에서 인증되지 않은 상태로 간주될 수 있습니다. 따라서, `authenticated` 속성을 `true`로 설정하거나, Spring Security의 인증 메커니즘을 통해 처리하는 것이 더 안전합니다.

---

### 언제 직접 인증 객체를 설정할까?

Spring Security는 대부분의 경우 인증과 권한 부여를 자동으로 처리합니다. 그러나 다음과 같은 상황에서는 직접 인증 객체를 설정하는 것이 유용할 수 있습니다:

1. **테스트 환경**: 테스트 시나리오에서 사용자를 수동으로 인증하여 특정 기능을 테스트하고 싶을 때.
2. **커스텀 인증 로직**: 기본 로그인 절차를 사용하지 않고, 외부 시스템(예: OAuth, JWT)을 이용해 인증을 처리할 때.
3. **기술적인 이유**: 특정한 로직에서 인증된 사용자 정보를 수동으로 설정해야 할 때.

---

### 결론

Spring Security에서 `Authentication`(인증)과 `Authorization`(권한 부여)은 애플리케이션 보안의 핵심입니다. 인증은 사용자가 누구인지 확인하는 과정이고, 권한 부여는 사용자가 할 수 있는 작업을 결정하는 과정입니다. 이를 이해함으로써, Spring Security를 더 효과적으로 활용할 수 있습니다. 직접 인증 객체를 설정하는 방법 또한 알아두면, 더 다양한 인증 시나리오를 처리할 수 있게 됩니다.
