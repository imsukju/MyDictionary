![MVC model](./Resource/MVC/MVC%20Model.png) 
출처 : https://www.yiiframework.com/doc/guide/1.1/en/basics.mvc


MVC(Model-View-Controller)는 소프트웨어 디자인 패턴 중 하나로, 애플리케이션의 비즈니스 로직과 사용자 인터페이스를 분리하여 개발과 유지 보수를 용이하게 합니다. 각 구성 요소는 특정 역할을 맡아 서로 독립적으로 동작하지만, 서로 협력하여 애플리케이션을 완성합니다. 이 패턴은 특히 웹 애플리케이션 개발에서 많이 사용되며, 사용자 경험을 향상시키고 코드의 재사용성을 높이는 데 기여합니다. 이제 MVC 모델의 각 구성 요소와 동작 방식을 자세히 설명드리겠습니다.

### 1. MVC의 개념
MVC는 세 가지 주요 컴포넌트로 구성됩니다:

1. **Model (모델)**  
   - 데이터와 비즈니스 로직을 담당하는 부분입니다. 애플리케이션의 상태(state)를 관리하고, 데이터베이스와 상호작용하거나 데이터를 가공하여 View나 Controller에 전달합니다.
   - 비즈니스 규칙을 기반으로 데이터를 처리하고, 그에 따른 결과를 계산하거나 반환하는 역할을 합니다.
   - 예를 들어, 사용자 계정 정보를 관리하는 `User` 객체나 주문 내역을 관리하는 `Order` 객체가 모델에 해당합니다.

2. **View (뷰)**  
   - 사용자 인터페이스를 담당하는 부분입니다. 사용자가 볼 수 있는 화면을 구성하며, 모델로부터 데이터를 받아 시각적으로 표현합니다.
   - 사용자가 입력한 값을 받아서 컨트롤러로 전달하고, 컨트롤러의 결과에 따라 화면을 갱신합니다.
   - 웹 애플리케이션에서는 HTML, CSS, JavaScript로 이루어진 웹 페이지가 View의 역할을 하며, 데스크톱 애플리케이션에서는 GUI 화면이 View의 역할을 합니다.

3. **Controller (컨트롤러)**  
   - 사용자의 입력을 처리하고, 모델과 뷰 사이의 상호작용을 관리하는 부분입니다.
   - 사용자의 요청(Request)을 받아서 해당 요청에 따라 모델을 업데이트하거나 필요한 데이터를 조회하고, 그 결과를 View에 전달하여 화면을 갱신하도록 합니다.
   - 웹 애플리케이션에서는 사용자의 HTTP 요청(GET, POST 등)을 받아 라우팅하여 적절한 모델과 뷰를 연결하는 역할을 합니다.

### 2. MVC의 흐름
MVC의 흐름을 이해하기 위해 단계별로 다음과 같은 시나리오를 생각해보겠습니다:

1. **사용자의 요청(Request)**  
   사용자가 웹 브라우저에서 `http://example.com/users/123` URL을 입력하여 사용자 정보를 조회한다고 가정해보겠습니다. 이때 웹 애플리케이션은 이 URL에 해당하는 HTTP 요청을 서버로 전달합니다.

2. **Controller 처리**  
   해당 요청을 컨트롤러가 받아들입니다. 예를 들어, `UserController`라는 컨트롤러가 요청을 수신하여 `showUser(int userId)`라는 메서드를 호출합니다. 이 메서드는 사용자 ID `123`을 매개변수로 받아 적절한 비즈니스 로직을 수행합니다.

3. **Model과의 상호작용**  
   컨트롤러는 모델(`User` 클래스)을 호출하여 사용자 ID `123`에 해당하는 사용자 데이터를 데이터베이스에서 조회합니다. 예를 들어, `User user = userService.findUserById(123)`이라는 코드가 있을 수 있습니다.

4. **View에 데이터 전달**  
   조회된 사용자 데이터를 모델로부터 받아온 후, 컨트롤러는 이 데이터를 `View`로 전달하여 시각적으로 표현합니다. `ModelAndView` 객체나 다른 전달 방식을 통해 View에 데이터를 넘기고, 적절한 템플릿 파일(`userProfile.html`)을 선택합니다.

5. **사용자에게 응답(Response)**  
   View는 사용자 데이터를 기반으로 HTML이나 다른 형식으로 화면을 구성하여 사용자에게 전달합니다. 브라우저는 이 데이터를 받아 사용자 프로필 화면을 출력합니다.

### 3. MVC의 각 컴포넌트의 세부 역할

#### 3.1 Model의 세부 역할
- **데이터 구조 정의**  
  객체의 속성(property)과 메서드(method)를 정의하여 애플리케이션의 상태를 표현합니다.
- **비즈니스 로직 구현**  
  데이터의 생성, 삭제, 수정, 조회와 관련된 비즈니스 로직을 구현합니다.
- **영속성 관리**  
  데이터베이스와의 상호작용을 통해 데이터를 저장하고 관리하는 역할을 합니다.

#### 3.2 View의 세부 역할
- **데이터의 시각적 표현**  
  모델로부터 전달받은 데이터를 화면에 출력하고, 사용자에게 정보를 제공합니다.
- **상호작용 처리**  
  사용자의 입력을 받아 컨트롤러로 전달하거나, 버튼 클릭, 폼 제출과 같은 이벤트를 처리합니다.
- **UI 업데이트**  
  사용자가 입력한 값에 따라 화면을 동적으로 업데이트합니다. JavaScript나 AJAX를 사용하여 화면을 갱신할 수 있습니다.

#### 3.3 Controller의 세부 역할
- **요청 분석**  
  사용자의 요청을 분석하여 어떤 작업을 수행할지 결정합니다. 예를 들어, 요청 URL이나 파라미터를 분석하여 적절한 메서드를 호출합니다.
- **모델 업데이트**  
  요청에 따라 모델의 상태를 변경하거나, 모델의 데이터를 조회합니다.
- **View와 상호작용**  
  모델의 데이터를 View에 전달하여 적절한 화면을 구성하도록 합니다.

### 4. MVC 구현 예제
다음은 Spring Framework를 사용하여 간단한 MVC 구조를 구현한 예제입니다.

```java
// 1. Model: 사용자 정보를 관리하는 클래스
public class User {
    private int id;
    private String name;
    private String email;

    // Constructor, Getter and Setter 생략
}

// 2. Controller: 사용자의 요청을 처리하는 컨트롤러 클래스
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class UserController {

    @GetMapping("/user")
    public String getUser(@RequestParam("id") int id, Model model) {
        User user = new User(id, "John Doe", "john@example.com");  // 예시 모델 데이터
        model.addAttribute("user", user);
        return "userProfile";  // "userProfile"이라는 View 이름 반환
    }
}

// 3. View: 사용자 정보를 출력하는 HTML 파일 (userProfile.html)
<!DOCTYPE html>
<html>
<head>
    <title>User Profile</title>
</head>
<body>
    <h1>User Profile</h1>
    <p>User ID: ${user.id}</p>
    <p>User Name: ${user.name}</p>
    <p>User Email: ${user.email}</p>
</body>
</html>
```

- 사용자가 `/user?id=123`을 요청하면 `UserController`가 이를 받아서 `User` 객체를 생성하고, View에 전달하여 사용자 프로필을 출력합니다.

### 5. MVC의 장점
- **구조화된 코드**  
  각 구성 요소가 명확히 분리되어 코드의 가독성과 유지 보수성이 높아집니다.
- **역할 분담**  
  개발자와 디자이너가 각자 자신에게 할당된 부분을 독립적으로 작업할 수 있습니다.
- **테스트 용이**  
  각 컴포넌트가 독립적으로 동작하기 때문에 단위 테스트(Unit Test)를 쉽게 작성할 수 있습니다.

### 6. MVC의 단점
- **복잡한 구조**  
  간단한 애플리케이션에서도 각 컴포넌트를 분리하여 작성해야 하므로 코드가 복잡해질 수 있습니다.
- **개발 속도 저하**  
  MVC의 규칙을 따르다 보면 개발 속도가 느려질 수 있으며, 작은 애플리케이션에서는 오히려 비효율적일 수 있습니다.

### 7. 결론
MVC 패턴은 애플리케이션의 구조를 체계적으로 정리하여 유지 보수와 확장을 용이하게 합니다. 특히, 대규모 웹 애플리케이션에서 이 패턴을 사용하면 여러 개발자가 동시에 협업하고, 사용자 인터페이스와 비즈니스 로직을 독립적으로 개선할 수 있습니다. 이로 인해 애플리케이션의 품질이 높아지고, 변경 사항이 발생할 때 코드의 영향을 최소화할 수 있습니다.

이상으로 MVC 패턴에 대한 기초적인 설명을 마치겠습니다. 도움이 되셨기를 바랍니다.