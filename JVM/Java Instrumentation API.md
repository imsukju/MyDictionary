### Java Instrumentation API 개요

**Instrumentation API**는 Java 플랫폼의 **`java.lang.instrument`** 패키지에서 제공되는 API로, JVM의 **클래스 로딩** 및 **런타임 동작**을 조작할 수 있는 강력한 기능을 제공합니다. 이 API는 주로 **성능 모니터링**, **프로파일링**, **코드 커버리지 도구** 또는 **AOP(Aspect-Oriented Programming)**와 같은 기술을 구현하는 데 사용됩니다. 이러한 도구들은 JVM의 **바이트코드 변환**을 통해 런타임에서 애플리케이션의 동작을 수정하거나 모니터링할 수 있습니다.

### 주요 기능
**Java Instrumentation**은 Java 가상 머신(JVM)의 클래스 로딩 및 런타임 동작을 **동적으로 수정**할 수 있는 API를 의미합니다.  
Instrumentation API는 클래스 로딩 및 실행 중인 클래스의 동작을 수정할 수 있는 다양한 기능을 제공합니다. 여기서 주요 기능을 정리해보겠습니다.

#### 1. **ClassFileTransformer**
- **ClassFileTransformer**는 클래스를 로드하기 전에 **바이트코드를 변환**할 수 있는 인터페이스입니다. 즉, 클래스 파일을 정의하는 과정에서 코드를 삽입하거나 수정할 수 있습니다.
- 이 기능을 사용하면, 예를 들어 **메소드 호출 전후에 로그**를 기록하거나, **메소드 실행 시간을 측정**하는 코드를 삽입할 수 있습니다. 바이트코드 수정은 **애플리케이션의 원본 코드**를 변경하지 않고도 수행될 수 있습니다.
- **ClassFileTransformer**는 JVM이 클래스를 로드할 때, 클래스 바이트코드를 전달받아 수정할 수 있습니다.

```java
public interface ClassFileTransformer
 {
    byte[] transform(ClassLoader loader, String className, 
                     Class<?> classBeingRedefined, ProtectionDomain protectionDomain, 
                     byte[] classfileBuffer) throws IllegalClassFormatException;
}
```
이 메서드를 통해 클래스가 로드될 때 바이트코드를 변환할 수 있으며, 변환된 바이트코드를 반환하게 됩니다.

#### 2. **Java Agent** [자세한 설명](/Java%20Agent.md)
- **Java Agent**는 Instrumentation API를 사용하는 주요 수단 중 하나입니다. Java 에이전트는 **JVM이 시작될 때** 특정 클래스를 로딩하기 전에 **ClassFileTransformer**를 등록하고, 이를 통해 모든 클래스의 바이트코드를 수정할 수 있습니다.
- 에이전트는 JVM을 시작할 때 **`-javaagent`** 옵션을 통해 등록됩니다. 이를 통해 **런타임 중에 클래스의 동작을 조작**할 수 있게 됩니다.
- Java Agent는 JVM에서 제공하는 **`premain`** 메서드를 통해 초기화되며, 이 메서드는 JVM 시작 전에 호출됩니다.

```java
public class MyAgent 
{
    public static void premain(String agentArgs, Instrumentation inst) {
        inst.addTransformer(new MyTransformer());
    }
}
```
- 이 메서드에서 전달되는 **`Instrumentation`** 객체는 클래스 파일의 바이트코드를 변환할 수 있는 도구를 제공합니다.

#### 3. **Retransformation (재변환)**
- Instrumentation API는 **이미 로드된 클래스**에 대해서도 바이트코드를 다시 변환할 수 있는 **재변환 기능**을 제공합니다.
- 즉, 애플리케이션이 실행 중인 상태에서도 클래스를 변환하고 수정할 수 있으며, 새로운 동작을 적용할 수 있습니다. **Retransformation**은 **JVM의 런타임**에 동적으로 클래스를 업데이트할 수 있는 유용한 기능입니다.

```java
public class MyAgent
 {
    public static void premain(String agentArgs, Instrumentation inst) {
        inst.addTransformer(new MyTransformer(), true);
    }
}
```
- **`true`** 값은 재변환을 허용하도록 설정하는 것입니다.

#### 4. **Redefinition (재정의)**
- Instrumentation API는 **이미 실행 중인 클래스**를 **재정의**하는 기능도 제공합니다. 이 기능은 `RedefineClasses` 메서드를 사용하여 **기존 클래스의 메소드나 필드**의 바이트코드를 새롭게 정의할 수 있습니다.
- 이는 실행 중인 애플리케이션의 클래스를 중단하지 않고도 **새로운 동작**을 적용할 수 있게 해줍니다.

```java
ClassDefinition definition = new ClassDefinition(clazz, byteCode);
inst.redefineClasses(definition);
```
- 이 코드를 통해 이미 로드된 클래스의 바이트코드를 재정의할 수 있습니다.

### JVM의 Instrumentation API 구현체

**JVM** 자체가 **Instrumentation API**의 **구현체**입니다. 즉, **JVM은 Instrumentation 인터페이스**를 구현하고 있으며, Java Agent 또는 다른 도구들이 이 API를 통해 **클래스 로딩** 및 **변환 작업**을 수행할 수 있도록 지원합니다.

- **Instrumentation 인터페이스**는 `java.lang.instrument` 패키지에 정의되어 있으며, 클래스 로딩 메커니즘에 깊이 통합되어 있습니다.
- **Java Agent**는 JVM이 제공하는 **Instrumentation API**를 사용하여 런타임 중 **바이트코드**를 조작하거나 변환할 수 있습니다.
- JVM은 Java Agent가 로드될 때 **premain 메서드**를 통해 Instrumentation 객체를 전달하며, 이를 통해 클래스 파일을 변환할 수 있는 환경을 제공합니다.

### Java Agent와 Instrumentation의 동작

Java Agent는 JVM에서 **Instrumentation API** 구현체에 접근하여 클래스 파일의 바이트코드를 변환하거나 변경합니다. 이때, JVM은 **Instrumentation** 객체를 Java Agent의 **premain 메서드**로 전달하고, 이를 통해 **클래스 로딩 과정에 개입**할 수 있게 됩니다.

Instrumentation API의 **구현체는 JVM** 자체에 포함되어 있으며, 이를 통해 애플리케이션이 런타임 중 동작을 **조작**할 수 있습니다.

### 사용 사례

1. **프로파일링 및 성능 모니터링**  
   - 메소드 호출 횟수, 실행 시간 등을 기록하기 위해 Instrumentation API를 활용하여 **코드를 삽입**할 수 있습니다. 이를 통해 성능을 분석하고 최적화할 수 있습니다.
  
2. **코드 커버리지 도구**  
   - 테스트 실행 중 어떤 코드가 실행되었는지 추적하기 위해 **바이트코드 수준에서 커버리지 정보를 추가**할 수 있습니다. 이를 통해 코드 테스트의 **효율성**을 높일 수 있습니다.

3. **AOP(Aspect-Oriented Programming) 구현**  
   - 특정 메소드 호출 전후에 **횡단 관심사**를 적용하기 위해 메소드 실행 전에 **추가 코드를 삽입**하는 방식으로 AOP를 구현할 수 있습니다. 예를 들어, 메서드 호출 전후로 **로깅**이나 **트랜잭션 관리**와 같은 기능을 추가할 수 있습니다.

4. **디버깅 도구**  
   - 실행 중인 애플리케이션에서 **특정 코드**를 동적으로 변경하거나 추가하여 버그를 **분석**할 수 있습니다. 이 기능을 통해 문제를 **런타임에서 추적**하고 해결하는 데 도움을 줄 수 있습니다.

### 요약

- **Instrumentation API**는 Java 플랫폼에서 **클래스 로딩 및 런타임 동작**을 **조작**할 수 있도록 제공되는 강력한 API입니다.
- 이를 통해 **프로파일링**, **코드 커버리지**, **AOP** 구현 등의 다양한 목적으로 **바이트코드를 변환**할 수 있습니다.
- **ClassFileTransformer**를 사용하여 클래스 로딩 시 **바이트코드를 수정**할 수 있으며, **Java Agent**를 통해 JVM이 시작될 때부터 이를 활용할 수 있습니다.
- **재변환** 및 **재정의** 기능을 통해 이미 로드된 클래스의 동작을 런타임에서 수정할 수 있습니다.
- 이러한 기능을 통해 **프로파일링**, **디버깅**, **AOP** 등 다양한 개발 도구에서 Instrumentation API를 효과적으로 사용할 수 있습니다.



### Java Instrumentation API와 Java Agent를 사용한 바이트코드 조작 예제

**Java Instrumentation API**는 애플리케이션의 **클래스 로딩**과 **바이트코드**를 **런타임**에 동적으로 조작할 수 있도록 도와주는 강력한 도구입니다. 이를 통해 개발자는 **성능 모니터링**, **프로파일링**, **코드 커버리지**, **AOP(Aspect-Oriented Programming)** 등을 구현할 수 있습니다.

이번 예제에서는 **Java Agent**와 **Instrumentation API**를 활용해, 클래스의 메서드가 실행될 때 **메서드 시작 부분에 로그**를 남기는 바이트코드 변환 작업을 수행합니다. 이를 위해 바이트코드를 조작하는 대표적인 라이브러리인 **ASM**을 사용합니다. ASM은 저수준 바이트코드 작업을 수행할 수 있도록 해주는 Java의 바이트코드 조작 라이브러리입니다.

### 예제 코드 설명

다음 예제에서는 **Java Agent**를 이용해, `MyTargetClass`의 모든 메서드의 시작 부분에 `System.out.println("Method entered")`라는 로그를 출력하는 코드를 삽입하는 방식을 구현했습니다.

```java
Class SimpleAgent
{
    public static void premain(String agentArgs, Instrumentation inst) 
    {
         inst.addTransformer(new SimpleTransformer());
    }
}

Class SimpleTransformer implements ClassFileTransformer 
{
    @Override
    public byte[] transform(ClassLoader loader, String className, Class<?> classBeingRedefined, ProtectionDomain protectionDomain, byte[] classfileBuffer) 
    {
         System.out.println("Transforming " + className);

         if (!className.equals("MyTargetClass")) 
         {
              // 특정 클래스(MyTargetClass)만 변환
              return classfileBuffer;
         }

         try 
         {
              ClassReader classReader = new ClassReader(classfileBuffer);
              ClassWriter classWriter = new ClassWriter(classReader, ClassWriter.COMPUTE_FRAMES);
              ClassVisitor classVisitor = new MyClassVisitor(Opcodes.ASM9, classWriter);
              classReader.accept(classVisitor, 0);
              return classWriter.toByteArray();
         } 
         catch (Exception e) 
         {
              e.printStackTrace();
              return classfileBuffer;  // 오류가 발생하면 원본 바이트코드를 반환
         }
    }
}

Class MyClassVisitor extends ClassVisitor 
{
    public MyClassVisitor(int api, ClassVisitor classVisitor) 
    {
         super(api, classVisitor);
    }

    @Override
    public MethodVisitor visitMethod(int access, String name, String descriptor, String signature, String[] exceptions) 
    {
         MethodVisitor mv = super.visitMethod(access, name, descriptor, signature, exceptions);
         return new MyMethodVisitor(Opcodes.ASM9, mv);
    }
}

Class MyMethodVisitor extends MethodVisitor 
{
    public MyMethodVisitor(int api, MethodVisitor methodVisitor) 
    {
         super(api, methodVisitor);
    }

    @Override
    public void visitCode() 
    {
         super.visitCode();
         // 메서드 시작 시 "Method entered" 로그 출력 추가
         mv.visitFieldInsn(Opcodes.GETSTATIC, "java/lang/System", "out", "Ljava/io/PrintStream;");
         mv.visitLdcInsn("Method entered");
         mv.visitMethodInsn(Opcodes.INVOKEVIRTUAL, "java/io/PrintStream", "println", "(Ljava/lang/String;)V", false);
    }
}
```

### 코드 설명

1. **`SimpleAgent`**  
   이 클래스는 **Java Agent**로, JVM이 시작될 때 `premain` 메서드가 호출됩니다. 이 메서드에서 **`SimpleTransformer`**를 JVM의 `Instrumentation` 객체에 등록하여, 클래스 로딩 시 **클래스 바이트코드를 변환**하도록 설정합니다.

2. **`SimpleTransformer`**  
   이 클래스는 `ClassFileTransformer` 인터페이스를 구현하여, 클래스가 로드될 때마다 바이트코드를 **변환**할 수 있게 합니다. `transform` 메서드에서는 특정 클래스(예시에서는 `MyTargetClass`)만을 타겟으로 변환을 수행하며, 변환이 필요 없는 클래스는 원본 바이트코드를 반환합니다.  
   변환할 클래스는 **ASM**의 `ClassReader`로 읽고, 변환된 결과는 `ClassWriter`로 출력됩니다.

3. **`MyClassVisitor`**  
   이 클래스는 **클래스의 메서드, 필드 등 구성 요소를 방문**하여 변환할 수 있도록 합니다. `visitMethod` 메서드를 통해 메서드를 방문하고, 변환을 위한 `MethodVisitor`를 반환합니다.

4. **`MyMethodVisitor`**  
   이 클래스는 **메서드의 바이트코드를 변환**하는 역할을 합니다. `visitCode` 메서드에서는 메서드가 호출될 때마다 "Method entered"라는 로그가 출력되도록 바이트코드를 추가합니다.

### 추가 설명

- **ASM 라이브러리**는 Java 바이트코드를 다루는 데 사용되며, 메서드의 바이트코드를 직접적으로 수정할 수 있게 해줍니다. 이 예제에서는 메서드의 시작 부분에 `System.out.println`을 삽입하는 방식으로 바이트코드를 수정하고 있습니다.
  
- **ClassReader**는 바이트코드에서 클래스를 읽고, **ClassWriter**는 변환된 바이트코드를 작성하는 데 사용됩니다.

- **`visitCode`** 메서드: 메서드의 첫 부분에 `System.out.println("Method entered")`가 삽입되도록 바이트코드를 추가합니다.

### Maven 의존성

이 예제를 실행하기 위해서는 **ASM 라이브러리**가 필요합니다. 이를 위해 Maven 프로젝트에 다음과 같은 의존성을 추가할 수 있습니다.

```xml
<dependency>
    <groupId>org.ow2.asm</groupId>
    <artifactId>asm</artifactId>
    <version>9.2</version>
</dependency>
```

또는 **Gradle**을 사용할 경우, 다음과 같이 의존성을 추가할 수 있습니다.

```gradle
implementation 'org.ow2.asm:asm:9.2'
```

### 요약

위 코드는 **Java Agent**를 통해 **클래스 로딩 시 바이트코드를 수정**하는 예제입니다. 특히 `MyTargetClass` 클래스의 모든 메서드 시작 부분에 **"Method entered"**라는 로그를 출력하는 코드를 삽입하는 방식으로 동작합니다. 이를 통해 **Instrumentation API**를 사용하여 **애플리케이션의 런타임 동작을 동적으로 수정**할 수 있다는 점을 알 수 있습니다.

**Java Instrumentation API**는 개발자가 애플리케이션의 동작을 정밀하게 제어하고 모니터링하는 데 중요한 도구이며, 주로 **프로파일링**, **성능 모니터링**, **AOP** 구현 등에 활용됩니다.