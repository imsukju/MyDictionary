
**2024_Memo** 폴더는 그날 공부한것을 순서에 상관없이 모아놓은 폴더입니다.  
그 외 다른 폴더는 위를 바탕으로 제 기준대로 다시 정리를 해놓았습니다.  
그날 공부한것을 **2024_Memo** 폴더에 넣지 않고 바로 그외의 폴더에 넣어놓을때도 있습니다.  

# 목차

<details>
<summary>1. Spring</summary>


- **Spring IOC**
    - [IOC 1](/Spring/Spring_IOC/IOC.md)
    - [IOC 2](/Spring/Spring_IOC/IOC2.md) **미완성**
    - [팩토리 빈 vs 빈 팩토리](/Spring/Spring_IOC/팩토리빈vs빈팩토리.md)
    - [Dependency Injection](/Spring/Spring_IOC/DependencyInjection.md)
    - [Instantiation Bean](/Spring/Spring_IOC/Instantiation_Bean.md)
- **AopAllience**
    - [델리게이트 정리](/Spring/AopAllience/델리게이트%20정리.md)
    - [동적 바인딩과 정적 바인딩](/Spring/AopAllience/동적바인딩%20과%20정적바인딩.md)
    - **SpringAop**
        - [Aop1](/Spring/AopAllience/SpringAop/Aop1.md)
        - [Aop2](/Spring/AopAllience/SpringAop/Aop2.md)
        - [Introduction](/Spring/AopAllience/SpringAop/Introduction.md)  
        - [Spring AOP에서의 프록시 메커니즘](/Spring/AopAllience/SpringAop/Spring%20AOP에서의%20프록시%20메커니즘.md)
    - **Aspectj** 
         > AopAllienc 의 Aspectj 는 Spring 프레임워크를 사용하지 않아도 독립적으로 사용할 수 있지만, 스프링의 빈으로 AspectJ 애스펙트(Aspect)를 관리할 수 있어서 Spring part 에 함께 작성했습니다.  
        - [Pointcut_And_methodmatches](/Spring/AopAllience/Aspectj/Pointcut_And_methodmatches.md)
        - [AspectJ Support1](Spring/AopAllience/Aspectj/AspectJ%20Support1.md)
        - [AspectJ Support2(Declaring Advice, Introductions1)](Spring/AopAllience/Aspectj/AspectJ%20Support2(Declaring%20Advice,%20Introductions1).md)
        - [AspectJ Support3](/Spring/AopAllience/Aspectj/AspectJ%20Support3.md)
        - [ProceedingJoinPoint](Spring/AopAllience/Aspectj/ProceedingJoinPoint.md)
        - [Spring AOP 및 AspectJ에서 다양한 포인트컷 표현식](Spring/AopAllience/Aspectj/Spring%20AOP%20및%20AspectJ에서%20다양한%20포인트컷%20표현식.md)
        - [Spring AOP와 AspectJ의 proceed() 메서드 동작 차이와 호환성 고려사항](Spring/AopAllience/Aspectj/Spring%20AOP와%20AspectJ의%20proceed()%20메서드%20동작%20차이와%20호환성%20고려사항.md)
        - [target vs within](Spring/AopAllience/Aspectj/target%20vs%20within.md)
      

    - **Java Dynamin Classes**
        - [Criteria_For_The_ProxyTargetClass](/Spring/AopAllience/JavaDynamicProxyClasses/Criteria_For_The_ProxyTargetClass.md)
        - [InvocationHandler](/Spring/AopAllience/JavaDynamicProxyClasses/InvocationHandler.md)
        - [JavaDynamicProxy](/Spring/AopAllience/JavaDynamicProxyClasses/JavaDynamicProxy.md)
        - [Serialization AND Methods Duplicated in Multiple Proxy Interfaces](/Spring/AopAllience/JavaDynamicProxyClasses/Serialization%20AND%20Methods%20Duplicated%20in%20Multiple%20Proxy%20Interfaces.md)        
        - [클래스 로딩과 관련된 제약조건](/Spring/AopAllience/JavaDynamicProxyClasses/클래스%20로딩과%20관련된%20제약조건.md)
- **Data Access**
    - [Transaction](./Spring/Data%20Access/Transaction.md)


</details>

<details>
<summary>JVM</summary>

  - [Java Instrumentation API](/JVM/Java%20Instrumentation%20API.md)
  - [Java Agent](/JVM/Java%20Agent.md)
  - [Instrumentation API와AspectJ](/JVM/Instrumentation%20API와AspectJ.md)
  - [Spring instrument library](/jvm/Spring%20instrument%20library.md)


</details>

<details>
<summary>JAVA</summary>

- **Refliection** 
    - [Non-reflective vs reflective](/JAVA/Refliection/Non-reflective%20vs%20reflective.md)
    - [reflection(Methods)](/JAVA/Refliection/reflection(Methods).md)

> 리플렉션 파트의 설명은 아직 준비가 되어있지 않습니다 빠르게 완성하겠습니다.

</details> 

<details>

<summary>JPA</summary>

- [1_엔티티와persis](./JPA/1_엔티티와persist.md)
- [2_Persistence Context와 플러시 메커니즘](./JPA/2_Persistence%20Context와%20플러시%20메커니즘.md)
- [3_엔티티%20매핑.md](./JPA/3_엔티티%20매핑.md)
- [4_연관관계 매핑](./JPA/4_연관관계%20매핑.md)
- [5_다대다 매핑과 복합키](./JPA/5_다대다%20매핑과%20복합키.md)

- **Tip**
    - [즉시로딩vs지연로딩 실행차이](./JPA/Tip/Tip1_즉시로딩vs지연로딩%20실행차이.md)
    - [연관관계 매핑 어노테이션 1](./JPA/Tip/Tip2_JPA%20연관관계%20매핑%20어노테이션1.md)
    - [Persistent Fields vs Persistent Properties](./JPA/Tip/Persistent%20Fields%20vs%20Persistent%20Properties.md)
    - [JPA 어노테이션 2](./JPA/Tip/Tip3_JPA%20%20어노테이션2.md)
</details>


<details>
<summary>TDD</summary>

- [TDD](/TDD/TDD.md)

- **Mockito**
    - [Mockito](/TDD/Mockito/Mockito.md)
    - [Mockito 와 DynamicProxy의 차이](/TDD/Mockito/Mockito%20와%20Dynamic%20Proxy%20차이.md)

</details>
