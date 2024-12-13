[해당문서](https://resilience4j.readme.io/docs/circuitbreaker)를 바탕으로 작성하였습니다.  

---

### CircuitBreaker

CircuitBreaker는 원격 서비스나 의존성이 실패했을 때, 장애를 빠르게 감지하고 시스템의 과부하를 방지하며, 나중에 호출을 다시 시도할 수 있도록 설계된 안정성 패턴입니다. Resilience4j는 이 패턴을 구현하여 마이크로서비스나 분산 시스템에서 유용하게 사용할 수 있도록 합니다.

---

### CircuitBreaker 상태

CircuitBreaker는 세 가지 상태를 가질 수 있습니다:

1. **Closed (닫힘):**
    - 모든 요청이 통과됩니다.
    - 실패율이 사전 정의된 임계값을 초과하면 Open 상태로 전환됩니다.
2. **Open (열림):**
    - 모든 요청이 차단됩니다.
    - Open 상태는 일정 시간 동안 유지됩니다. 이 시간이 지나면 CircuitBreaker는 Half-Open 상태로 전환됩니다.
3. **Half-Open (반열림):**
    - 제한된 수의 요청만 허용됩니다.
    - 요청이 성공적으로 처리되면 Closed 상태로 전환됩니다.
    - 요청이 실패하면 다시 Open 상태로 돌아갑니다.

---

### CircuitBreaker 설정

CircuitBreaker는 다양한 설정 옵션을 제공합니다:

- **Failure Rate Threshold (실패율 임계값):**
    
    실패율이 이 값(예: 50%)을 초과하면 CircuitBreaker는 Open 상태로 전환됩니다.
    
- **Wait Duration in Open State (Open 상태의 대기 시간):**
    
    Open 상태에서 Half-Open 상태로 전환되기 전까지의 대기 시간입니다.
    
- **Permitted Number of Calls in Half-Open State (Half-Open 상태에서 허용되는 요청 수):**
    
    Half-Open 상태에서 성공 여부를 테스트하기 위해 허용되는 요청의 수입니다.
    
- **Sliding Window Type (슬라이딩 윈도우 유형):**
    
    실패율 계산 방식으로, 시간 기반(Time-based) 또는 호출 수 기반(Count-based) 중 하나를 선택할 수 있습니다.
    
- **Sliding Window Size (슬라이딩 윈도우 크기):**
    
    실패율 계산을 위한 윈도우의 크기입니다.
    

---

### CircuitBreaker 생성

CircuitBreaker는 Java에서 다음과 같이 생성할 수 있습니다:

```java
CircuitBreakerConfig circuitBreakerConfig = CircuitBreakerConfig.custom()
    .failureRateThreshold(50) // 실패율 50%
    .waitDurationInOpenState(Duration.ofSeconds(60)) // Open 상태 유지 시간
    .slidingWindowType(SlidingWindowType.COUNT_BASED) // 호출 수 기반 윈도우
    .slidingWindowSize(100) // 윈도우 크기
    .build();

CircuitBreaker circuitBreaker = CircuitBreaker.of("backendService", circuitBreakerConfig);

```

---

### CircuitBreaker 사용 예시

CircuitBreaker를 활용하여 요청을 데코레이션하고, 실패 시 대체 응답(Fallback)을 지정할 수 있습니다:

```java
Supplier<String> decoratedSupplier = CircuitBreaker
    .decorateSupplier(circuitBreaker, backendService::call);

String result = Try.ofSupplier(decoratedSupplier)
    .recover(throwable -> "Fallback Response") // 장애 발생 시 대체 응답
    .get();

```

---

### CircuitBreaker 이벤트 모니터링

CircuitBreaker는 상태 변경, 실패율 초과 등의 이벤트를 기록하거나 모니터링 시스템과 통합할 수 있습니다:

```java
circuitBreaker.getEventPublisher()
    .onStateTransition(event -> logger.info(event.toString())) // 상태 변경 이벤트
    .onFailureRateExceeded(event -> logger.info(event.toString())) // 실패율 초과 이벤트
    .onCallNotPermitted(event -> logger.info(event.toString())); // 허용되지 않은 호출 이벤트

```

---

### CircuitBreaker와 스레드 안전성

Resilience4j의 CircuitBreaker는 완전히 스레드 안전(Thread-safe)하며, 가볍고 빠르게 설계되었습니다. 이는 애플리케이션의 성능에 부정적인 영향을 최소화합니다.

---

### 주요 특징 요약

- 장애 탐지 및 전파 방지.
- 재시도 시점 및 실패율 관리.
- 간단한 구성과 통합 가능.
- 이벤트 로깅 및 모니터링 지원.

---

위 내용은 **CircuitBreaker 패턴의 원리와 Resilience4j에서 이를 구현하는 방법**을 설명합니다. 이 라이브러리는 안정성과 성능 최적화를 동시에 고려하여, 마이크로서비스 또는 분산 환경에서 자주 사용됩니다.