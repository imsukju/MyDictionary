### Java Proxy에서 중복된 메서드 처리

Java의 Proxy 클래스를 사용할 때, 두 개 이상의 인터페이스에 동일한 이름과 파라미터 시그니처를 가진 메서드가 포함되어 있으면, 프록시 클래스의 인터페이스 순서가 중요한 역할을 합니다. 이 글에서는 이러한 상황에서 프록시가 어떻게 동작하는지, 그리고 주의해야 할 사항들을 쉽게 설명해보겠습니다.

#### 1. 동일한 메서드를 가진 인터페이스

프록시 클래스가 여러 인터페이스를 구현할 때, 동일한 이름과 파라미터 시그니처를 가진 메서드가 중복으로 존재할 수 있습니다. 예를 들어, `InterfaceA`와 `InterfaceB` 모두 `duplicateMethod()`라는 메서드를 포함하고 있다면, 프록시 인스턴스에서 이 메서드를 호출할 때 어느 인터페이스의 메서드가 호출될지에 대해 알아야 합니다.

```java
interface InterfaceA {
    void duplicateMethod();
}

interface InterfaceB {
    void duplicateMethod();
}
```

#### 2. 프록시의 동작 원리

프록시 인스턴스에서 중복 메서드가 호출되면, 해당 메서드를 포함한 **첫 번째 인터페이스**의 `Method` 객체가 `Invocation Handler`의 `invoke` 메서드에 전달됩니다. 따라서, 프록시 인터페이스의 순서에 따라 어떤 구현체가 호출될지 결정됩니다.

다음은 두 인터페이스를 구현한 클래스와 그들을 핸들링하는 Invocation Handler입니다.

```java
class ClassA implements InterfaceA {
    @Override
    public void duplicateMethod() {
        System.out.println("ClassA: duplicateMethod");
    }
}

class ClassB implements InterfaceB {
    @Override
    public void duplicateMethod() {
        System.out.println("ClassB: duplicateMethod");
    }
}

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

class MyInvocationHandler implements InvocationHandler {
    private final Object objA;
    private final Object objB;

    public MyInvocationHandler(Object objA, Object objB) {
        this.objA = objA;
        this.objB = objB;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("Invoked method: " + method.getName());

        if (method.getDeclaringClass().isAssignableFrom(InterfaceA.class)) {
            return method.invoke(objA, args);
        } else if (method.getDeclaringClass().isAssignableFrom(InterfaceB.class)) {
            return method.invoke(objB, args);
        }

        return null;
    }
}
```

이제 두 인터페이스를 구현한 프록시 인스턴스를 생성하고, 중복 메서드를 호출하는 예제를 살펴보겠습니다.

```java
import java.lang.reflect.Proxy;

public class DynamicProxyExample {
    public static void main(String[] args) {
        InterfaceA classA = new ClassA();
        InterfaceB classB = new ClassB();

        Object proxyInstance = Proxy.newProxyInstance(
                DynamicProxyExample.class.getClassLoader(),
                new Class<?>[]{InterfaceA.class, InterfaceB.class},
                new MyInvocationHandler(classA, classB)
        );

        ((InterfaceA) proxyInstance).duplicateMethod();  // ClassA: duplicateMethod 출력
        ((InterfaceB) proxyInstance).duplicateMethod();  // ClassA: duplicateMethod 출력
    }
}
```

이 코드에서 `proxyInstance`는 `InterfaceA`와 `InterfaceB`를 동시에 구현한 프록시 객체입니다. 주의할 점은 `duplicateMethod()`가 `InterfaceA`와 `InterfaceB` 모두에 존재함에도 불구하고, 항상 `ClassA`의 구현이 호출된다는 점입니다. 이는 프록시 생성 시 `InterfaceA`가 먼저 지정되었기 때문입니다.

#### 4. Object 메서드와 프록시

프록시 인터페이스에 `java.lang.Object`의 `hashCode`, `equals`, `toString` 메서드와 동일한 이름과 시그니처를 가진 메서드가 포함된 경우, 이 메서드들이 프록시에서 호출되면 `java.lang.Object`가 선언 클래스인 `Method` 객체가 `Invocation Handler`에 전달됩니다. 이는 Java의 기본 동작 방식에 따라 `Object`의 메서드가 우선적으로 처리되기 때문입니다.

#### 5. 예외 처리 주의사항

만약 중복된 메서드가 다양한 예외를 던질 수 있는 상황이라면, `Invocation Handler`에서 던질 수 있는 체크드 예외는 호출된 인터페이스에서 정의된 예외 타입 중 하나여야 합니다. 그렇지 않으면 `UndeclaredThrowableException`이 발생할 수 있습니다. 이는 프록시가 예상치 못한 예외를 던졌을 때 발생하는 예외입니다.

#### 6. 결론

Java의 Proxy를 사용할 때는 인터페이스의 순서와 중복된 메서드가 어떻게 처리되는지에 대해 명확히 이해해야 합니다. 이 글에서는 동일한 메서드가 포함된 인터페이스들을 프록시로 처리하는 방법과 주의해야 할 사항들을 설명했습니다. Proxy를 사용할 때 이러한 사항들을 염두에 두고, 원하는 동작이 제대로 수행되도록 코드를 작성하세요.



### Serialization
Java의 Proxy는 런타임에 동적으로 인터페이스를 구현하는 객체를 생성할 수 있는 기능입니다. 이 글에서는 Proxy와 관련된 직렬화(Serialization) 개념을 쉽게 설명하고, 예제를 통해 Proxy를 효과적으로 사용하는 방법을 알아보겠습니다.

#### 1. Proxy와 Serializable 인터페이스

Java의 `java.lang.reflect.Proxy` 클래스는 기본적으로 `java.io.Serializable` 인터페이스를 구현합니다. 따라서 Proxy 인스턴스는 직렬화가 가능합니다. 직렬화란 객체를 바이트 스트림으로 변환하여 파일로 저장하거나 네트워크를 통해 전송할 수 있게 하는 과정입니다.

하지만 Proxy 인스턴스에 포함된 Invocation Handler가 직렬화할 수 없는 객체라면, 직렬화 시 `java.io.NotSerializableException` 예외가 발생할 수 있습니다. 이때는 주의가 필요합니다. Invocation Handler란, Proxy를 통해 호출되는 메서드들을 처리하는 역할을 하는 객체입니다.

**핵심 요점**:
- Proxy 클래스는 직렬화 가능한 필드를 가지고 있지 않으며, `serialVersionUID`는 항상 `0L`로 설정됩니다.
- 모든 Proxy 클래스의 Class 객체는 직렬화 가능합니다.
- Proxy가 `java.io.Externalizable`을 구현하면, `Serializable`을 구현하는 것과 동일한 효과를 가질 수 있습니다.

#### 2. Proxy의 직렬화 과정

Java의 직렬화 프로세스에서 Proxy 클래스의 설명자는 특별한 방식으로 직렬화됩니다. 이를 위해 `ObjectOutputStream`이 사용됩니다. Proxy 클래스는 `Proxy.isProxyClass()` 메서드를 통해 프록시인지 확인되고, 직렬화 시 특정 형식으로 직렬화됩니다.

프록시 클래스는 기본적으로 `java.lang.reflect.Proxy`의 서브클래스이기 때문에, Proxy 클래스는 슈퍼클래스로 `Proxy`를 포함합니다. 이렇게 함으로써 프록시 인스턴스의 진화된 직렬화 표현을 지원할 수 있습니다.

#### 3. Proxy와 Invocation Handler 사용 예제

다음은 Proxy를 사용하여 메서드 호출 전후에 메시지를 출력하는 간단한 예제입니다.

```java
public interface HelloWorld {
    void sayHello();
    void sayGoodbye();
    void greet(String name);
    void setLanguage(String language);
    String getGreeting();
}

public class HelloWorldImpl implements HelloWorld {
    private String language = "English";
    private String greeting = "Hello";

    @Override
    public void sayHello() {
        System.out.println(greeting + ", world!");
    }

    @Override
    public void sayGoodbye() {
        System.out.println("Goodbye, world!");
    }

    @Override
    public void greet(String name) {
        System.out.println(greeting + ", " + name + "!");
    }

    @Override
    public void setLanguage(String language) {
        this.language = language;
        if ("English".equalsIgnoreCase(language)) {
            this.greeting = "Hello";
        } else if ("Spanish".equalsIgnoreCase(language)) {
            this.greeting = "Hola";
        } else if ("French".equalsIgnoreCase(language)) {
            this.greeting = "Bonjour";
        } else {
            this.greeting = "Hello"; 
        }
    }

    @Override
    public String getGreeting() {
        return greeting;
    }
}
```

위 인터페이스와 클래스는 간단한 인사말 프로그램을 구현합니다. 이제 Proxy와 Invocation Handler를 사용하여 메서드 호출 전후에 메시지를 출력해보겠습니다.

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class MyInvocationHandler implements InvocationHandler {
    private Object target;

    public MyInvocationHandler(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("Before method: " + method.getName());
        Object result = method.invoke(target, args);
        System.out.println("After method: " + method.getName());
        return result;
    }
}

import java.lang.reflect.Proxy;

public class Program {
    public static void main(String[] args) {
        HelloWorld proxyInstance = (HelloWorld) Proxy.newProxyInstance(
            HelloWorldImpl.class.getClassLoader(),
            new Class<?>[]{HelloWorld.class},
            new MyInvocationHandler(new HelloWorldImpl())
        );
        
        proxyInstance.sayHello();  // "Before method: sayHello", "Hello, world!", "After method: sayHello"
        proxyInstance.sayGoodbye(); // "Before method: sayGoodbye", "Goodbye, world!", "After method: sayGoodbye"
    }
}
```

이 코드에서는 `MyInvocationHandler`가 프록시 인스턴스에 전달되어, 메서드 호출 전후에 메시지를 출력합니다.

#### 4. 추가적인 Proxy 예제

예를 들어, 특정 인터페이스의 메서드 호출을 디버그하기 위해 프록시를 사용할 수도 있습니다. 아래는 이를 구현한 간단한 코드입니다.

```java
public interface Foo {
    Object bar(Object obj) throws Exception;
}

public class FooImpl implements Foo {
    public Object bar(Object obj) throws Exception {
        if (obj == null) {
            throw new Exception("Input object cannot be null.");
        }
        System.out.println("Executing FooImpl's bar method");
        return obj;
    }
}

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class DebugProxy implements InvocationHandler {
    private Object target;

    public static Object newInstance(Object target) {
        return Proxy.newProxyInstance(
            target.getClass().getClassLoader(),
            target.getClass().getInterfaces(),
            new DebugProxy(target));
    }

    private DebugProxy(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("Before method: " + method.getName());
        try {
            return method.invoke(target, args);
        } catch (Exception e) {
            System.out.println("Exception caught: " + e.getMessage());
            throw e;
        } finally {
            System.out.println("After method: " + method.getName());
        }
    }
}

public class Program {
    public static void main(String[] args) throws Exception {
        Foo foo = (Foo) DebugProxy.newInstance(new FooImpl());
        foo.bar(null); // "Before method: bar", "Exception caught: Input object cannot be null.", "After method: bar"
    }
}
```

이 예제에서 `DebugProxy`는 메서드 호출 전후에 로그를 남기고, 예외가 발생할 경우 이를 출력합니다.

#### 5. 결론

Java의 Proxy와 직렬화는 매우 강력한 기능을 제공하며, 동적인 인터페이스 구현 및 런타임 로깅, 예외 처리 등에 유용하게 사용될 수 있습니다. 이 글에서는 Proxy와 직렬화의 기본 개념과 함께 간단한 예제를 통해 이를 이해하는 데 도움을 주었습니다. 이를 통해 Proxy를 다양한 상황에서 어떻게 활용할 수 있는지 감을 잡으셨길 바랍니다.