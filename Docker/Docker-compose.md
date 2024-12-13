### **1. 기본 구조**

```yaml
version: "3.8"  # Compose 파일의 버전
services:       # 서비스 정의 섹션
  web:          # 서비스 이름
    image: nginx:latest  # 사용할 Docker 이미지
    ports:
      - "8080:80"        # 호스트:컨테이너 포트 매핑
    volumes:
      - ./html:/usr/share/nginx/html # 호스트 디렉토리와 컨테이너 디렉토리 연결
    environment:
      - NGINX_HOST=localhost          # 환경 변수 설정
    depends_on:
      - app                          # 'app' 서비스가 먼저 실행되어야 함
  app:
    build:
      context: ./app                 # Dockerfile의 경로
    volumes:
      - ./app:/app                   # 볼륨 마운트
    command: python app.py           # 컨테이너 시작 시 실행할 명령어
networks:
  default:
    driver: bridge                  # 네트워크 드라이버 정의
volumes:
  app-data:                         # 볼륨 이름 정의
```

---

### **2. 주요 요소**

#### **2.1. `version`**
- Compose 파일의 버전을 명시합니다.
- 사용 가능한 버전: `1`, `2`, `3.x` (현재 대부분 `3.x`를 사용).
- 버전에 따라 지원하는 기능이 달라집니다.

#### **2.2. `services`**
- 애플리케이션을 구성하는 서비스(컨테이너)를 정의합니다.
- 각 서비스는 컨테이너에 대한 설정을 포함합니다.

**예:**
```yaml
services:
  web:
    image: nginx:latest
```

#### **2.3. `image`**
- 사용할 Docker 이미지를 지정합니다. (예: `nginx:latest`, `mysql:5.7`)
- `build` 키를 사용하면 직접 Dockerfile로 이미지를 빌드 가능.

#### **2.4. `build`**
- Docker 이미지를 빌드할 정보를 제공.
- `context`: Dockerfile의 경로.
- `dockerfile`: 특정 Dockerfile 이름을 지정.

**예:**
```yaml
build:
  context: ./app
  dockerfile: CustomDockerfile
```

#### **2.5. `ports`**
- 호스트와 컨테이너 간의 포트 매핑.
- `"호스트 포트:컨테이너 포트"` 형식으로 지정.

**예:**
```yaml
ports:
  - "8080:80"
```

#### **2.6. `volumes`**
- 호스트와 컨테이너 간 디렉토리(또는 파일) 공유.
- 데이터 영속성을 유지하는 데 유용.

**예:**
```yaml
volumes:
  - ./data:/var/lib/mysql
```

#### **2.7. `environment`**
- 환경 변수를 설정.
- `KEY=VALUE` 형식.

**예:**
```yaml
environment:
  - NODE_ENV=production
```

#### **2.8. `depends_on`**
- 특정 서비스가 다른 서비스보다 먼저 시작되도록 설정.
- 단, `healthcheck` 기능은 포함되지 않음(서비스 준비 여부 보장은 아님).

**예:**
```yaml
depends_on:
  - db
```

#### **2.9. `networks`**
- 네트워크를 정의하고 서비스 간 통신을 구성.

**예:**
```yaml
networks:
  default:
    driver: bridge
```

#### **2.10. `volumes` (전역)**
- 전역 볼륨을 정의.
- 모든 서비스에서 공유 가능.

**예:**
```yaml
volumes:
  app-data:
```

---

### **3. 고급 기능**

#### **3.1. `command`**
- 컨테이너 시작 시 실행할 명령어를 설정.

**예:**
```yaml
command: python app.py
```

#### **3.2. `entrypoint`**
- 컨테이너 시작 시 실행될 기본 명령을 설정.
- `command`와 함께 사용할 수 있음.

#### **3.3. `restart`**
- 컨테이너 재시작 정책을 정의.
- 옵션: `no`, `always`, `on-failure`, `unless-stopped`.

**예:**
```yaml
restart: always
```

#### **3.4. `healthcheck`**
- 서비스의 상태 확인.

**예:**
```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 30s
  timeout: 10s
  retries: 5
```

#### **3.5. `secrets`**
- 민감한 데이터를 외부 파일로 관리.

**예:**
```yaml
secrets:
  my_secret:
    file: ./secret.txt
```

#### **3.6. `deploy`**
- Docker Swarm 관련 배포 설정.

**예:**
```yaml
deploy:
  replicas: 3
  resources:
    limits:
      cpus: "0.5"
      memory: "512M"
```

---

### **4. 전체 예제**

```yaml
version: "3.8"

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
    depends_on:
      - app

  app:
    build:
      context: ./app
    volumes:
      - ./app:/app
    command: python app.py
    environment:
      - APP_ENV=production
    networks:
      - custom-network

networks:
  custom-network:
    driver: bridge

volumes:
  app-data:
```

---

### **5. Best Practices**
1. **파일 버전 관리:** `version`은 최신 안정 버전을 사용 (`3.8` 추천).
2. **환경 변수 관리:** 민감한 데이터는 `.env` 파일로 관리.
3. **네트워크 및 볼륨:** 서비스 간 데이터를 효율적으로 공유.
4. **가독성:** 설정 파일을 계층적으로 정리하여 가독성을 높임.
5. **의존성 관리:** `depends_on`을 사용해 실행 순서 정의.
