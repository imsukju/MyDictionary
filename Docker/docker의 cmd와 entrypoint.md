Docker에서 `CMD`와 `ENTRYPOINT`는 컨테이너가 실행될 때 수행할 기본 명령어를 정의하는데 사용됩니다. 둘은 유사해 보이지만, 동작 방식과 목적이 다릅니다. 다음은 두 개의 명령어를 **매우 자세히** 설명한 내용입니다.

---

## 1. **CMD**

`CMD`는 컨테이너가 실행될 때 수행할 **기본 명령어** 또는 **인수를 지정**합니다.

### **주요 특징**

- **기본 명령어**를 설정하며, 이를 덮어쓸 수 있습니다.
- Dockerfile당 **한 번만** 정의할 수 있습니다.
    - 여러 개의 `CMD`가 있으면 마지막에 선언된 `CMD`만 유효합니다.
- `docker run` 명령으로 실행 시 **명령을 덮어쓸 수 있습니다.**
- 주로 **컨테이너의 기본 동작**을 정의하지만, 특정 상황에서 유연하게 사용하려고 할 때 유용합니다.

### **CMD의 작성 형식**

1. **Shell 형식 (권장하지 않음)**
    - 기본 명령어를 쉘 명령어처럼 정의합니다.
        
        ```
        CMD echo "Hello World"
        
        ```
        
        - 위 명령은 기본적으로 `/bin/sh -c`를 통해 실행됩니다.
        - 즉, 실제로는 `/bin/sh -c 'echo "Hello World"'`가 실행됩니다.
2. **Exec 형식 (권장됨)**
    - JSON 배열로 명령어를 정의합니다.
        
        ```
        CMD ["echo", "Hello World"]
        
        ```
        
        - 쉘을 거치지 않고 바로 명령어가 실행되므로 더 안전합니다.
3. **인수만 제공하는 형식**
    - `ENTRYPOINT`와 조합하여 인수만 지정합니다.
        
        ```
        CMD ["--default-arg"]
        
        ```
        

### **CMD의 사용 사례**

- 컨테이너 실행 시 제공된 명령이 없으면 `CMD`가 기본적으로 실행됩니다.
- 예:
    
    ```
    FROM ubuntu
    CMD ["echo", "This is the default command"]
    
    ```
    
    실행:
    
    ```bash
    docker run my-image
    
    ```
    
    출력:
    
    ```
    This is the default command
    
    ```
    
    그러나, 실행 명령이 제공되면 덮어씁니다.
    
    ```bash
    docker run my-image echo "Overridden Command"
    
    ```
    
    출력:
    
    ```
    Overridden Command
    
    ```
    

---

## 2. **ENTRYPOINT**

`ENTRYPOINT`는 컨테이너가 실행될 때 수행할 **고정 명령어**를 정의합니다.

### **주요 특징**

- 컨테이너가 **항상 수행해야 하는 명령어**를 설정합니다.
- Dockerfile당 **한 번만** 정의할 수 있습니다.
    - 여러 개의 `ENTRYPOINT`가 있으면 마지막에 선언된 것만 유효합니다.
- `docker run`에서 명령어를 덮어쓸 수 없으며, **고정 동작**을 보장합니다.
- `CMD`와 조합하여 **고정 명령어 + 기본 인수** 형태로 동작시킬 수 있습니다.

### **ENTRYPOINT의 작성 형식**

1. **Shell 형식 (권장하지 않음)**
    
    ```
    ENTRYPOINT echo "Hello World"
    
    ```
    
    - `/bin/sh -c`를 통해 실행됩니다.
2. **Exec 형식 (권장됨)**
    
    ```
    ENTRYPOINT ["echo", "Hello World"]
    
    ```
    
    - 쉘을 거치지 않고 바로 실행됩니다.
3. **스크립트 실행 형식**
    - 복잡한 초기화를 처리할 때 사용합니다.
        
        ```
        COPY entrypoint.sh /usr/local/bin/
        ENTRYPOINT ["sh", "/usr/local/bin/entrypoint.sh"]
        
        ```
        

### **ENTRYPOINT의 사용 사례**

- 컨테이너 실행 시 반드시 실행되어야 하는 명령어가 있을 때 사용합니다.
- 예:
    
    ```
    FROM ubuntu
    ENTRYPOINT ["echo", "This is a fixed command:"]
    
    ```
    
    실행:
    
    ```bash
    docker run my-image
    
    ```
    
    출력:
    
    ```
    This is a fixed command:
    
    ```
    
    명령어를 추가 제공:
    
    ```bash
    docker run my-image "additional message"
    
    ```
    
    출력:
    
    ```
    This is a fixed command: additional message
    
    ```
    

---

## 3. **CMD와 ENTRYPOINT의 차이점**

| 구분 | CMD | ENTRYPOINT |
| --- | --- | --- |
| **목적** | 기본 명령어 제공 (Optional) | 고정 명령어 제공 (Required) |
| **덮어쓰기** | `docker run`에서 덮어쓸 수 있음 | 덮어쓸 수 없음 (`--entrypoint`로 변경 가능) |
| **조합 가능 여부** | 단독 또는 ENTRYPOINT와 조합하여 사용 가능 | CMD와 조합하여 기본 인수 전달 가능 |
| **실행 방식** | 유연한 실행 가능성 제공 | 실행 방식 고정화 |

---

## 4. **CMD와 ENTRYPOINT 조합**

둘을 조합하면 강력한 컨테이너 동작을 설정할 수 있습니다.

### 조합 예제:

```
FROM ubuntu

ENTRYPOINT ["echo", "Message:"]
CMD ["Default message"]

```

### 실행 결과:

1. 기본 실행:
    
    ```bash
    docker run my-image
    
    ```
    
    출력:
    
    ```
    Message: Default message
    
    ```
    
2. `CMD` 덮어쓰기:
    
    ```bash
    docker run my-image "Overridden CMD"
    
    ```
    
    출력:
    
    ```
    Message: Overridden CMD
    
    ```
    

---

## 5. **CMD와 ENTRYPOINT 활용 가이드**

- **CMD**
    - 컨테이너가 실행될 때 기본적으로 실행되지만 덮어쓰는 것이 자주 필요한 경우 사용.
    - 예: 다양한 인수를 사용해야 하는 CLI 도구 컨테이너.
- **ENTRYPOINT**
    - 컨테이너의 동작이 고정된 경우 사용.
    - 예: 특정 서비스(예: Nginx, MySQL 등)를 실행하는 컨테이너.
- **CMD + ENTRYPOINT**
    - 고정된 명령어에 추가로 기본 옵션이나 인수를 제공해야 하는 경우 사용.
    - 예: 고정된 실행 스크립트에 다양한 옵션을 추가로 전달해야 하는 경우.

---

필요하다면 추가 예제를 제공하거나 상세히 분석해 드릴게요!