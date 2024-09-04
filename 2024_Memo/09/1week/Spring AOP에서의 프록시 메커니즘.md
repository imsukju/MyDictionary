### Spring AOP에서의 프록시 메커니즘

Spring AOP는 지정된 타겟 객체에 대해 **JDK 동적 프록시** 또는 **CGLIB**을 사용하여 프록시를 생성합니다. 이 두 가지 프록시 방식은 각기 다른 상황에서 사용되며, 어떤 프록시가 사용될지는 타겟 객체의 특성에 따라 결정됩니다.

#### JDK 동적 프록시
- **JDK 동적 프록시**는 JDK에 내장되어 있으며, 타겟 객체가 **인터페이스를 하나 이상 구현한 경우**에 사용됩니다.
- 이 경우, 타겟 객체가 구현한 **모든 인터페이스**가 프록시 처리됩니다.

#### CGLIB 프록시
- 타겟 객체가 **인터페이스를 구현하지 않는 경우**, **CGLIB 프록시**가 사용됩니다.
- CGLIB은 **일반적인 오픈 소스 클래스 정의 라이브러리**로, Spring Core에 재패키징되어 포함되어 있습니다.

#### CGLIB 프록시 강제 사용
- **CGLIB 프록시**를 **강제로 사용**하고 싶다면, XML 설정에서 `<aop:config>` 요소의 `proxy-target-class` 속성을 **true**로 설정해야 합니다. 이 설정을 통해 타겟 객체의 **모든 메서드**(인터페이스 메서드뿐만 아니라 타겟 객체에 정의된 모든 메서드)가 프록시 처리됩니다.

```xml
<aop:config proxy-target-class="true">
    <!-- 다른 빈 정의 -->
</aop:config>
```

또는 **@AspectJ 자동 프록시 지원**을 사용하는 경우, `<aop:aspectj-autoproxy>` 요소에서 `proxy-target-class` 속성을 **true**로 설정할 수 있습니다.

```xml
<aop:aspectj-autoproxy proxy-target-class="true"/>
```

### CGLIB 사용 시 고려 사항

1. **final 메서드**는 CGLIB이 생성한 프록시에서 **오버라이드**할 수 없습니다. 따라서 **조언**을 적용할 수 없습니다.
2. **Spring 4.0** 이후에는 CGLIB 프록시로 인해 **생성자가 두 번 호출**되지 않습니다. 이는 CGLIB 프록시 인스턴스가 **Objenesis**를 통해 생성되기 때문입니다.
3. **JDK 9+**에서는 CGLIB 프록시 사용이 제한될 수 있습니다. 특히 모듈 시스템을 사용할 때 `java.lang` 패키지의 클래스를 프록시로 생성할 수 없습니다. 이 문제를 해결하려면 JVM 부트스트랩 플래그 `--add-opens=java.base/java.lang=ALL-UNNAMED`를 사용할 수 있지만, 이는 모든 모듈에서 지원되지 않습니다.

### 프록시를 이용한 메서드 호출

프록시를 사용하지 않는 일반적인 객체 참조에서의 메서드 호출은 객체 자신에서 직접 이루어집니다.

```java
public class SimplePojo implements Pojo 
{

    public void foo() 
    {
        // 'this' 참조로 직접 호출
        this.bar();
    }

    public void bar() 
    {
        // 일부 로직
    }
}
```

위의 코드에서 `foo()` 메서드에서 `bar()`를 호출하면, **단순히 객체 내부**에서 **직접 호출**되는 형태입니다.

```java
public class Main 
{

    public static void main(String[] args) 
    {
        Pojo pojo = new SimplePojo();
        // pojo 객체에서 foo() 메서드 호출
        pojo.foo();
    }
}
```

#### 프록시를 통한 메서드 호출

Spring AOP에서 프록시를 사용하면 상황이 달라집니다. 메서드 호출이 프록시 객체를 통해 이루어지고, 그 프록시는 필요한 **조언**을 적용하게 됩니다.

```java
public class Main 
{

    public static void main(String[] args) 
    {
        ProxyFactory factory = new ProxyFactory(new SimplePojo());
        factory.addInterface(Pojo.class);
        factory.addAdvice(new RetryAdvice());

        Pojo pojo = (Pojo) factory.getProxy();
        // 프록시를 통해 메서드 호출
        pojo.foo();
    }
}
```

여기서 주목할 점은 **클라이언트 코드**에서 프록시를 참조한다는 것입니다. 메서드 호출이 프록시를 통해 이루어지므로, 프록시는 해당 호출에 관련된 **조언(Interceptor)**을 적용할 수 있습니다. 하지만, 타겟 객체가 자기 자신에게 호출하는 경우(`this.bar()` 등)는 **프록시를 우회**하여 호출됩니다. 따라서 이 경우 **조언**이 적용되지 않습니다.

### 자기 호출(self-invocation) 문제 해결

자기 호출이 발생하면 AOP 조언이 적용되지 않는 문제가 있습니다. 이를 해결하기 위해서는 **코드를 리팩터링**하여 자기 호출을 피하는 것이 좋습니다. 하지만, 일부 경우에는 Spring AOP의 내부 메커니즘을 사용하여 해결할 수 있습니다.

```java
public class SimplePojo implements Pojo 
{

    public void foo() 
    {
        // AOP 프록시를 강제로 호출
        ((Pojo) AopContext.currentProxy()).bar();
    }

    public void bar() 
    {
        // 일부 로직
    }
}
```

이 방법은 Spring AOP에 코드를 강하게 결합시키는 좋지 않은 방법입니다. 자기 호출 문제를 피하기 위해 이와 같은 방식은 **권장되지 않습니다**.

#### 자기 호출 문제를 피하는 방법

만약 이 방법을 사용하려면, `ProxyFactory`에서 `setExposeProxy(true)`를 설정해야 합니다.

```java
public class Main 
{

    public static void main(String[] args) 
    {
        ProxyFactory factory = new ProxyFactory(new SimplePojo());
        factory.addInterface(Pojo.class);
        factory.addAdvice(new RetryAdvice());
        factory.setExposeProxy(true);

        Pojo pojo = (Pojo) factory.getProxy();
        // 프록시를 통해 메서드 호출
        pojo.foo();
    }
}
```

### AspectJ와의 차이점

**AspectJ**는 **프록시 기반의 AOP 프레임워크가 아니므로**, 자기 호출 문제를 가지고 있지 않습니다. AspectJ는 컴파일 타임에 메서드 호출을 조작하기 때문에 이러한 문제를 회피할 수 있습니다.

---

### 요약

- Spring AOP는 **JDK 동적 프록시** 또는 **CGLIB**을 사용하여 프록시를 생성합니다.
- 타겟 객체가 **인터페이스를 구현**하면 JDK 동적 프록시가 사용되고, 그렇지 않으면 CGLIB 프록시가 사용됩니다.
- CGLIB 프록시는 **모든 메서드**를 프록시 처리하지만, **final 메서드**에는 조언을 적용할 수 없습니다.
- **자기 호출(self-invocation)**은 프록시를 우회하기 때문에, 조언이 적용되지 않는 문제가 있습니다.
  - 이를 해결하려면 코드를 리팩터링하거나, 필요시 `AopContext`를 사용할 수 있지만, 이는 좋지 않은 방법입니다.
- **AspectJ**는 이러한 자기 호출 문제를 가지고 있지 않습니다.