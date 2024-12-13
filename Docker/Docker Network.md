도커 네트워크(Docker Networking)는 컨테이너 간 통신, 컨테이너와 호스트 간 통신, 컨테이너와 외부 네트워크 간 통신을 관리하는 핵심적인 기능입니다. 아래는 도커 네트워크의 모든 것을 극도로 자세히 설명한 내용입니다.

---

## 1. **도커 네트워크의 기본 개념**
도커 네트워크는 컨테이너와 다른 시스템(컨테이너, 호스트, 외부 네트워크) 간의 통신을 제공하며, 이를 통해 애플리케이션의 확장성과 격리성을 관리합니다. 도커는 기본적으로 5가지 네트워크 드라이버를 제공합니다.

- **bridge**: 기본 네트워크로, 컨테이너를 하나의 브리지 네트워크에 연결합니다.
- **host**: 호스트 네트워크를 컨테이너와 공유합니다.
- **none**: 네트워크를 완전히 비활성화합니다.
- **overlay**: 여러 호스트에 걸쳐 네트워크를 생성하여 분산 환경에서 사용됩니다.
- **macvlan**: 컨테이너에 물리적 NIC처럼 동작하는 인터페이스를 제공합니다.

---

## 2. **도커 네트워크 드라이버 종류**

### 2.1. **Bridge Network**
- **용도**: 컨테이너 간 격리된 네트워크를 제공.
- **구조**: 
  - Docker는 `docker0`라는 브리지 네트워크를 생성.
  - 동일 브리지에 연결된 컨테이너는 서로 통신 가능.
- **특징**:
  - NAT(Network Address Translation)를 통해 외부와 통신.
  - 컨테이너가 기본적으로 IP를 할당받음(예: `172.17.x.x`).
- **예제**:
  ```bash
  docker network create my_bridge
  docker run --network=my_bridge --name=my_container nginx
  ```

### 2.2. **Host Network**
- **용도**: 성능이 중요한 경우(컨테이너 네트워크를 생략하고 호스트 네트워크를 그대로 사용).
- **구조**: 컨테이너는 호스트의 네트워크 스택을 공유.
- **특징**:
  - 컨테이너가 고유의 IP를 가지지 않음.
  - 컨테이너 포트가 호스트 포트와 충돌 가능.
- **예제**:
  ```bash
  docker run --network=host nginx
  ```

### 2.3. **None Network**
- **용도**: 네트워크를 완전히 비활성화.
- **구조**: 네트워크 인터페이스가 없음.
- **특징**:
  - 네트워크 격리가 필요한 테스트 또는 분석용.
- **예제**:
  ```bash
  docker run --network=none nginx
  ```

### 2.4. **Overlay Network**
- **용도**: 여러 호스트에 걸친 컨테이너 간 통신.
- **구조**:
  - Docker Swarm, Kubernetes 등에서 사용.
  - VXLAN을 사용하여 네트워크를 가상화.
- **특징**:
  - Swarm 클러스터 환경에서 동작.
  - 외부 구성 없이 클라우드 네이티브 지원.
- **예제**:
  ```bash
  docker network create -d overlay my_overlay
  ```

### 2.5. **Macvlan Network**
- **용도**: 컨테이너에 직접 물리적 NIC처럼 동작하는 네트워크를 제공.
- **구조**:
  - 컨테이너가 고유의 MAC 주소와 IP를 받음.
  - 네트워크 트래픽을 컨테이너별로 분리 가능.
- **특징**:
  - 기존 네트워크와의 통합에 유용.
  - 복잡한 네트워크 구성 가능.
- **예제**:
  ```bash
  docker network create -d macvlan \
      --subnet=192.168.1.0/24 \
      --gateway=192.168.1.1 \
      -o parent=eth0 my_macvlan
  ```

---

## 3. **도커 네트워크 명령어**

### 3.1. 네트워크 생성
```bash
docker network create \
  --driver bridge \
  --subnet 192.168.1.0/24 \
  my_custom_network
```

### 3.2. 네트워크 목록 조회
```bash
docker network ls
```

### 3.3. 네트워크 상세 정보 확인
```bash
docker network inspect my_custom_network
```

### 3.4. 네트워크 삭제
```bash
docker network rm my_custom_network
```

---

## 4. **컨테이너와 네트워크 연결**

### 4.1. 컨테이너 시작 시 네트워크 연결
```bash
docker run --network=my_custom_network --name=my_app nginx
```

### 4.2. 실행 중인 컨테이너를 네트워크에 연결
```bash
docker network connect my_custom_network my_app
```

### 4.3. 실행 중인 컨테이너를 네트워크에서 분리
```bash
docker network disconnect my_custom_network my_app
```

---

## 5. **도커 네트워크 고급 설정**

### 5.1. 네트워크 격리
- 브리지 네트워크를 사용하여 다른 브리지 네트워크와 격리.
- **예제**:
  ```bash
  docker network create isolated_network
  docker run --network=isolated_network --name=app1 nginx
  ```

### 5.2. 네트워크 보안
- **IPTables**를 통한 네트워크 제어.
- **Docker Compose**로 컨테이너 간 통신 제한.

### 5.3. DNS 지원
- Docker는 자체 DNS 서버를 사용하여 컨테이너 이름 기반 통신 가능.
- 컨테이너는 자동으로 `/etc/resolv.conf`에 Docker DNS를 포함.

---

## 6. **실제 활용 사례**

### 6.1. 여러 컨테이너 연결
- 애플리케이션이 여러 컨테이너로 분리된 경우:
  - 예: 프론트엔드, 백엔드, 데이터베이스.
  ```yaml
  version: "3.8"
  services:
    app:
      image: my_app
      networks:
        - app_network
    db:
      image: postgres
      networks:
        - app_network
  networks:
    app_network:
      driver: bridge
  ```

### 6.2. 외부 네트워크와 통합
- Macvlan을 사용하여 기존 네트워크와 통합:
  ```bash
  docker network create -d macvlan \
      --subnet=192.168.1.0/24 \
      --gateway=192.168.1.1 \
      -o parent=eth0 my_external_network
  ```

### 6.3. 클러스터 환경에서 네트워크 구성
- Overlay 네트워크를 사용하여 클러스터 기반 애플리케이션 구축.
  ```bash
  docker network create -d overlay my_swarm_network
  ```

---

도커 네트워크는 단순한 로컬 개발 환경부터 복잡한 분산 시스템에 이르기까지 매우 유연한 네트워크 솔루션을 제공합니다. 각 드라이버와 설정 옵션을 적절히 활용하면 높은 성능과 보안을 모두 만족하는 네트워크 아키텍처를 설계할 수 있습니다.