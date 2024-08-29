Spring AOP에서 `Introduction`은 기존 객체에 새로운 인터페이스와 메소드를 동적으로 추가하는 기능을 말합니다. 이는 AOP의 핵심 기능 중 하나로, 기존 클래스나 객체에 대해 런타임에서 추가적인 기능을 구현할 수 있도록 합니다. 

### `Introduction`의 정의

`Introduction`은 Spring AOP의 `IntroductionAdvisor`를 통해 구현되며, 특정 인터페이스를 프록시 객체에 추가하는 방식으로 동작합니다. 이 방식은 기존 객체의 코드나 디자인을 변경하지 않고도 새로운 기능을 추가할 수 있게 해줍니다.

### Introduction Advice
AOP(Aspect-Oriented Programming)의 "Introduction Advice"는 **특정 클래스나 객체에 새로운 메서드나 필드(속성)를 추가하기 위한 기능입니다.** 
이는 AOP의 주요 기능 중 하나로, 기존의 코드에 수정 없이 새로운 기능을 주입할 수 있게 해줍니다. 이를 통해 코드의 재사용성을 높이고, 중복을 줄이며, 특정 관심사를 모듈화하는 데 큰 도움이 됩니다.
### Introduction Advice의 주요 개념

1. **관심사의 분리(Separation of Concerns)**:
    - AOP의 가장 중요한 목표는 서로 다른 관심사를 코드에서 분리하는 것입니다. 
    이를 통해 비즈니스 로직과는 별개의 관심사(예: 로깅, 보안, 트랜잭션 관리)를 독립적으로 관리할 수 있습니다. Introduction Advice는 기존의 클래스에 새로운 기능을 동적으로 추가함으로써 이러한 관심사를 모듈화하고 코드의 복잡성을 줄이는 역할을 합니다.

2. **타겟 클래스(Target Class)**:
    - Introduction Advice가 적용되는 대상 클래스입니다. 이 클래스는 AOP 프레임워크에 의해 동적으로 확장되며, 원본 클래스의 소스 코드를 수정하지 않고도 새로운 메서드와 속성을 제공받게 됩니다. 
    타겟 클래스는 보통 특정 인터페이스를 구현하지 않는 기존 클래스이지만, Introduction Advice를 통해 런타임에 그 인터페이스를 구현하도록 만들어질 수 있습니다.

3. **인터페이스 구현(Implementing an Interface)**:
    - Introduction Advice의 핵심은 기존 클래스에 새로운 인터페이스를 구현시키는 것입니다. 예를 들어, 기존 클래스가 `Lockable`이라는 인터페이스를 구현하지 않더라도, Introduction Advice를 사용하면 해당 클래스가 해당 인터페이스를 구현하도록 할 수 있습니다. [1^]
    이는 클래스에 새로운 메서드와 필드를 추가하는 방식으로 이루어지며, 이러한 메서드와 필드는 런타임에 동적으로 제공됩니다.

4. **AOP 프레임워크의 역할**:
    - AOP 프레임워크는 Introduction Advice를 통해 대상 클래스에 새로운 메서드와 인터페이스를 추가하는 역할을 합니다. 이는 주로 프록시 패턴을 통해 구현되며, 프록시는 타겟 클래스를 감싸고 그 클래스의 메서드 호출을 가로채어 필요한 새로운 기능을 제공합니다. 
    클라이언트 코드에서는 이러한 변화가 투명하게 처리되며, 실제로는 수정된 기능을 사용하는 것입니다.


###  Introduction Advice 장점

1. **유연성(Flexibility)**:
    - Introduction Advice는 코드의 구조를 변경하지 않고도 새로운 기능을 추가할 수 있게 해줍니다. 이는 특히 다양한 클래스에 공통된 기능을 추가해야 할 때 매우 유용합니다.

2. **모듈화(Modularity)**:
    - 새로운 기능을 별도의 모듈로 분리하여 관리할 수 있습니다. 이를 통해 코드의 유지보수성을 크게 높일 수 있습니다.

3. **재사용성(Reusability)**:
    - 동일한 기능을 여러 클래스에 쉽게 적용할 수 있습니다. 예를 들어, 여러 클래스에 공통적으로 필요한 기능을 Introduction Advice를 통해 추가하면, 코드 중복을 줄이고 관리가 수월해집니다.

4. **코드 수정 없이 확장 가능(Extensibility)**:
    - 기존의 코드를 전혀 수정하지 않고도, 런타임에 동적으로 기능을 추가할 수 있습니다. 이는 특히 코드 변경이 어렵거나 불가능한 상황에서 유용합니다.




### MethodInterceptor와 Introduction의 관계

AOP에서 **MethodInterceptor**는 메서드 호출을 가로채서 추가적인 로직을 실행할 수 있는 기능을 제공합니다. Spring AOP에서 **MethodInterceptor**의 `invoke()` 메서드를 통해 Introduction을 구현할 수 있습니다.

- **`invoke()` 메서드**는 메서드 호출이 도입된 인터페이스의 메서드인지 확인한 후, 해당 메서드를 처리합니다. 중요한 점은 도입된 메서드를 처리할 때, `proceed()` 메서드를 호출하지 않는다는 것입니다. 이는 `proceed()`가 원래의 메서드 호출을 진행하도록 하기 때문인데, 도입된 인터페이스의 메서드는 원래의 메서드가 없기 때문에 `proceed()`를 호출할 수 없습니다.

### IntroductionAdvisor와 IntroductionInterceptor

**`IntroductionAdvisor`**와 **`IntroductionInterceptor`**는 Introduction 기능을 구현하고 관리하는 핵심적인 역할을 합니다.

#### IntroductionAdvisor의 역할

**IntroductionAdvisor**는 특정 클래스에 `Introduction`을 적용하기 위한 설정을 제공합니다. 이 클래스는 `Introduction` 기능을 관리하며, 특정 객체에 어떤 인터페이스를 추가할지, 그리고 이를 어떤 클래스에 적용할지를 결정합니다. 또한, 도입할 인터페이스의 유효성을 검사하여, 설정 오류를 방지하고 도입이 예상대로 작동하도록 보장합니다.

### 주요 역할: 

1. **도입할 인터페이스 정의**:
   - **getInterfaces() 메서드**: 이 메서드는 타겟 객체에 추가할 인터페이스를 지정합니다. 예를 들어, 특정 객체에 `Auditable` 인터페이스를 추가하려면, 이 메서드는 `Auditable` 인터페이스를 반환합니다. 이 메서드를 통해 도입할 인터페이스를 명시적으로 정의할 수 있습니다.

2. **타겟 클래스 필터링**:
   - **getClassFilter() 메서드**: 이 메서드는 `Introduction`이 적용될 대상 클래스를 필터링하는 역할을 합니다. 예를 들어, 특정 패키지에 속한 클래스에만 도입을 적용하고자 할 때, 이 메서드를 사용하여 해당 패키지에 속한 클래스만 필터링할 수 있습니다. 이를 통해 특정 조건을 만족하는 클래스에만 인터페이스 도입이 적용되도록 제어할 수 있습니다.

3. **인터페이스 유효성 검사**:
   - **validateInterfaces() 메서드**: 이 메서드는 도입할 인터페이스가 실제로 대상 클래스에서 구현될 수 있는지를 검증합니다. 이는 설정 오류를 방지하고, 시스템의 안정성을 높이는 중요한 단계입니다. 예를 들어, 도입하려는 인터페이스가 특정 클래스에서 충돌 없이 구현될 수 있는지, 또는 도입이 필요한 환경을 갖추고 있는지를 확인합니다.

>
#### IntroductionInterceptor의 역할

`IntroductionInterceptor`는 도입된 인터페이스의 실제 동작을 처리하는 핵심 구성 요소입니다. Spring AOP에서는 이 기능을 손쉽게 구현할 수 있도록 **DelegatingIntroductionInterceptor**라는 클래스를 제공합니다. 이 클래스는 도입된 인터페이스의 메서드 호출을 다른 객체(Delegate)에게 위임함으로써, 인터페이스 도입과 메서드 호출 처리 과정을 간소화합니다.

1. **메서드 호출 가로채기**:
   - **invoke() 메서드**: 도입된 인터페이스의 메서드가 호출되면, `IntroductionInterceptor`는 이 호출을 가로채고 적절히 처리합니다. 예를 들어, `Auditable` 인터페이스가 도입된 객체에서 `setAuditInfo()` 메서드가 호출되면, 이 호출은 `invoke()` 메서드에 의해 가로채어지고 처리됩니다. 이는 메서드 호출에 대한 추가적인 로직을 주입할 수 있는 중요한 지점입니다.

2. **도입된 인터페이스 구현**:
   - `IntroductionInterceptor`는 도입된 인터페이스의 메서드를 실제로 구현하거나, 필요한 경우 delegate 객체를 통해 이 작업을 수행합니다. 이를 통해 도입된 인터페이스가 예상대로 작동하도록 보장합니다. 만약 원래의 객체가 도입된 인터페이스의 메서드를 지원하지 않는 경우, 이 인터셉터는 해당 메서드 호출을 delegate에게 위임하여 처리할 수 있습니다.

3. **원하지 않는 인터페이스 억제**:
   - **suppressInterface(Class intf) 메서드**: delegate 객체가 구현했지만, AOP 프록시에 노출되지 말아야 할 인터페이스를 억제할 수 있습니다. 이를 통해 노출할 인터페이스를 세밀하게 제어할 수 있으며, 보안상 또는 디자인상 노출을 제한하고자 하는 인터페이스를 숨길 수 있습니다.


[1^]:
### 실제 사례: Lockable 인터페이스 도입

여기서는 `Lockable`이라는 인터페이스를 도입하여 객체가 잠금 기능을 가지도록 하는 예를 들어보겠습니다.

1. **Lockable 인터페이스**: 이 인터페이스는 객체를 잠그거나 잠금 해제할 수 있는 기능을 정의합니다.

   ```java
   public interface Lockable {
       void lock();
       void unlock();
       boolean locked();
   }
   ```

2. **LockMixin 클래스**: 이 클래스는 `Lockable` 인터페이스를 구현하고, `DelegatingIntroductionInterceptor`를 확장하여 Introduction 기능을 제공합니다. 이 클래스는 객체가 잠겨 있는 동안 특정 메서드 호출을 막는 기능을 추가합니다.

   - `locked` 상태 변수는 객체의 잠금 상태를 관리합니다.
   - `invoke()` 메서드는 메서드 호출을 가로채고, 잠겨 있는 동안 `set` 메서드가 호출되면 예외를 발생시킵니다.


    ```java
    public class LockMixin extends DelegatingIntroductionInterceptor implements Lockable {

        private boolean locked;

        @Override
        public void lock() {
            this.locked = true;
        }

        @Override
        public void unlock() {
            this.locked = false;
        }

        @Override
        public boolean locked() {
            return this.locked;
        }

        // `invoke()` 메서드는 메서드 호출을 가로채고, 잠겨 있는 동안 `set` 메서드가 호출되면 예외를 발생시킵니다.
        @Override
        public Object invoke(MethodInvocation invocation) throws Throwable {
            // `IntroductionInterceptor` 역할: 도입된 인터페이스의 메서드 호출을 처리
            if (locked() && invocation.getMethod().getName().startsWith("set")) {
                // 객체가 잠겨 있을 때 `set` 메서드가 호출되면 `LockedException`을 발생시킵니다.
                throw new LockedException();
            }
            // `invocation.proceed()` -> 원래 객체(target class)의 메서드에 호출을 위임합니다.
            return super.invoke(invocation);
        }

    }
    ```
     


3. **LockMixinAdvisor 클래스**: `LockMixin`을 사용하여 `Lockable` 인터페이스를 도입하는 설정을 제공합니다. 이 어드바이저는 별도의 설정이 필요 없으며, 각 객체에 맞는 인스턴스를 관리합니다.
    ```java
    public class LockMixinAdvisor extends DefaultIntroductionAdvisor {
        public LockMixinAdvisor() {
            // LockMixin이 IntroductionInterceptor 역할을 하고,
            // Lockable 인터페이스가 대상 클래스에 도입되도록 설정합니다.
            super(new LockMixin(), Lockable.class);
        }
    }
    // **`DefaultIntroductionAdvisor`**: 이 클래스는 `IntroductionAdvisor`의 기본 구현체로, 
    // 특정 인터페이스를 프록시에 추가할 수 있도록 지원합니다.

    // **`LockMixin`**: 이 객체는 `DelegatingIntroductionInterceptor`를 통해 `IntroductionInterceptor` 역할을 구현합니다. 
    // `IntroductionInterceptor`는 메서드 호출 시 추가적인 작업을 수행하는 코드입니다.

    // **`Lockable.class`**: 이 클래스는 프록시가 `Lockable` 인터페이스를 구현하도록 지시합니다.
    // Introduction 기능을 통해 프록시 객체에 새로운 인터페이스를 도입할 수 있습니다.
    ```

- **`DefaultIntroductionAdvisor`**: 이 클래스는 `IntroductionAdvisor`의 기본 구현체로, 특정 인터페이스를 프록시에 추가할 수 있도록 지원합니다.
- **`LockMixin`**: 이 객체는 `Advice`를 구현합니다. `Advice`는 메서드 호출 시 추가적인 작업을 수행하는 코드입니다.
- **`Lockable.class`**: 이 클래스는 프록시가 `Lockable` 인터페이스를 구현하도록 지시합니다.

이 생성자는 `LockMixinAdvisor`가 `Lockable` 인터페이스를 프록시 객체에 적용하도록 설정합니다.


### 4. 프록시 객체의 타입

- **프록시와 인터페이스**: `ProxyFactory`는 `LockMixinAdvisor`를 사용하여 `MyTargetClass`의 프록시를 생성합니다. `LockMixinAdvisor`에 의해 `Lockable` 인터페이스가 프록시에 추가됩니다. 따라서 생성된 프록시는 `Lockable` 인터페이스를 구현하게 됩니다.
- **`getProxy()`**: 프록시 객체를 반환합니다. 이 프록시 객체는 원래의 `MyTargetClass` 객체를 감싸며, `Lockable` 인터페이스를 구현합니다.

`ProxyFactory`를 사용한 프록시 생성
```java
@Bean
public MyTargetClass myTargetClass(LockMixinAdvisor lockMixinAdvisor) {
    ProxyFactory factory = new ProxyFactory(new MyTargetClass());
    factory.addAdvisor(lockMixinAdvisor); // LockMixinAdvisor를 프록시에 추가하여 Lockable 인터페이스를 도입합니다.
    factory.setProxyTargetClass(true);  // CGLIB를 사용하여 프록시 생성 (기본 클래스의 모든 메서드를 프록시화)
    MyTargetClass proxy = (MyTargetClass) factory.getProxy();

    // 프록시 객체의 타입 확인
    if (proxy instanceof Lockable) {
        System.out.println("프록시 객체는 Lockable 인터페이스를 구현합니다.");
    } else {
        System.out.println("프록시 객체는 Lockable 인터페이스를 구현하지 않습니다.");
    }

    return proxy;
}
```

- **`ProxyFactory`**: Spring의 AOP 프레임워크에서 제공하는 클래스입니다. 프록시 객체를 생성할 때 사용합니다.
- **`addAdvisor(lockMixinAdvisor)`**: 프록시에 `LockMixinAdvisor`를 추가합니다. 이 어드바이저는 `LockMixin`을 `Advice`로 사용하고, `Lockable` 인터페이스를 프록시에 추가합니다.
- **`setProxyTargetClass(true)`**: CGLIB를 사용하여 프록시를 생성합니다. 이는 `MyTargetClass`의 서브클래스를 생성하여 메서드 호출을 가로챕니다.

### 5. 타입 캐스팅 가능성

- **프록시 객체와 `Lockable` 인터페이스**: 생성된 프록시 객체는 `Lockable` 인터페이스를 구현하므로, `Lockable` 타입으로 캐스팅할 수 있습니다. 이는 `LockMixinAdvisor`가 `Lockable` 인터페이스를 프록시에 적용했기 때문입니다.
- **프록시의 타입**: 프록시는 `MyTargetClass`의 서브클래스로 생성되며, `Lockable` 인터페이스를 구현합니다. 이로 인해 프록시 객체는 `Lockable` 타입으로 캐스팅할 수 있습니다.

### 요약

1. **`LockMixinAdvisor`**: `DefaultIntroductionAdvisor`를 상속받아 `Lockable` 인터페이스를 프록시에 추가합니다.
2. **프록시 생성**: `ProxyFactory`를 사용하여 `MyTargetClass`의 프록시를 생성하며, `LockMixinAdvisor`를 통해 `Lockable` 인터페이스를 프록시에 적용합니다.
3. **캐스팅 가능**: 생성된 프록시 객체는 `Lockable` 인터페이스를 구현하므로 `Lockable` 타입으로 캐스팅할 수 있습니다.

### 결론

**Introduction**은 AOP에서 객체에 새로운 기능을 동적으로 추가할 수 있는 강력한 도구입니다. Spring AOP는 **IntroductionAdvisor**와 **IntroductionInterceptor**를 통해 이러한 기능을 쉽게 구현하고 관리할 수 있도록 지원합니다. 특히, **DelegatingIntroductionInterceptor**는 도입된 인터페이스의 메서드 호출을 간편하게 처리할 수 있게 해주며, 객체의 기능을 확장하는 데 매우 유용합니다. 이를 통해 코드의 모듈화와 재사용성이 높아지고, 시스템의 유지보수성과 확장성이 크게 향상됩니다.
