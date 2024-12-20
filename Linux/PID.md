**PID**(Process ID)는 **프로세스 식별자**로, 운영체제에서 실행 중인 각 프로세스를 고유하게 식별하기 위해 부여되는 숫자입니다. 모든 프로세스는 실행될 때 운영체제에 의해 고유한 PID를 할당받습니다.

---

### PID의 역할
1. **프로세스 관리**:
   - 운영체제가 프로세스를 관리하고 제어할 때, PID를 사용하여 특정 프로세스를 구분합니다.
   - 예를 들어, `kill`, `ps`, `top` 명령에서 PID를 통해 특정 프로세스를 지정할 수 있습니다.

2. **부모-자식 관계 파악**:
   - PID는 프로세스 계층 구조에서 부모-자식 관계를 이해하는 데 사용됩니다.
   - 각 프로세스에는 `PPID`(Parent Process ID)라는 부모 프로세스의 PID가 있습니다.

3. **고유성**:
   - 같은 시간에 실행 중인 두 프로세스가 동일한 PID를 가질 수 없습니다.
   - 하지만 프로세스가 종료된 후 해당 PID는 다시 재활용될 수 있습니다.

---

### PID의 특징
1. **숫자 값**:
   - PID는 정수 값이며, 일반적으로 1부터 시작하여 시스템에서 허용하는 최대 값까지 증가합니다.

2. **PID 1 (init/systemd)**:
   - PID 1은 리눅스 시스템에서 **최초로 실행되는 프로세스**입니다.
   - 과거에는 `init`이 이 역할을 했고, 현대 시스템에서는 `systemd`가 PID 1로 동작합니다. 이 프로세스는 다른 모든 프로세스의 부모 역할을 합니다.

3. **동적 할당**:
   - 새로운 프로세스가 생성될 때 사용 가능한 가장 작은 PID를 운영체제가 할당합니다.

4. **프로세스 종료 후 재활용**:
   - 프로세스가 종료되면 해당 PID는 다른 프로세스에 재사용될 수 있습니다.

---

### PID를 확인하는 방법
터미널에서 PID를 확인할 수 있습니다.

1. **`ps` 명령**:
   - 현재 실행 중인 프로세스를 확인합니다.
   ```bash
   ps -e
   ```
   출력 예시:
   ```
     PID TTY          TIME CMD
       1 ?        00:00:01 systemd
     100 ?        00:00:00 sshd
     200 ?        00:00:00 bash
     300 ?        00:00:10 python
   ```

2. **`top` 또는 `htop`**:
   - 실시간으로 실행 중인 프로세스를 모니터링하며 PID를 확인할 수 있습니다.

3. **`$BASHPID` (쉘 환경 변수)**:
   - 현재 쉘의 PID를 확인할 수 있습니다.
   ```bash
   echo $BASHPID
   ```

4. **프로그램 실행 시**:
   - 프로그램 실행 직후 `$!`로 PID를 확인할 수 있습니다.
   ```bash
   some_command &
   echo $!
   ```

---

### 프로세스 예시
- **PID 1**: 시스템에서 최초로 실행된 프로세스 (예: `systemd`).
- **사용자 프로세스**: 터미널에서 실행한 모든 명령은 고유한 PID를 가집니다.
- **데몬 프로세스**: 백그라운드에서 실행 중인 서비스(예: `sshd`)도 PID를 가집니다.

---

### 한 문장으로 정리
PID는 운영체제가 프로세스를 식별하고 관리하기 위해 사용하는 **고유한 숫자 ID**입니다. PID는 프로세스의 생명 주기 동안 유일하며, 프로세스가 종료된 후 재활용될 수 있습니다.