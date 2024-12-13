

## 1. **`FROM`**
- **설명**: 베이스 이미지를 정의하는 명령어로, 모든 Dockerfile은 반드시 하나의 `FROM` 명령으로 시작해야 합니다.
- **형식**: 
  ```dockerfile
  FROM <이미지>:<태그>
  ```
- **예제**:
  ```dockerfile
  FROM ubuntu:20.04
  ```
  - `ubuntu:20.04`를 기반으로 이미지를 생성.
  - 여러 `FROM`을 사용하면 **멀티 스테이지 빌드** 구현 가능.

---

## 2. **`LABEL`**
- **설명**: 이미지에 메타데이터를 추가합니다.
- **형식**:
  ```dockerfile
  LABEL <키>=<값> <키2>=<값2>
  ```
- **예제**:
  ```dockerfile
  LABEL maintainer="you@example.com" version="1.0" description="My Docker Image"
  ```
  - 이미지를 관리하는 사람, 버전 정보, 설명을 메타데이터로 추가.

---

## 3. **`ARG`**
- **설명**: 빌드 시 사용할 **빌드 시점 변수**를 정의합니다. `docker build` 명령어에서 값을 전달할 수 있습니다.
- **형식**:
  ```dockerfile
  ARG <변수_이름>=<기본값>
  ```
- **예제**:
  ```dockerfile
  ARG VERSION=1.0
  FROM myimage:${VERSION}
  ```
  - `docker build` 시 `--build-arg VERSION=2.0`처럼 값을 전달할 수 있음.
  - 기본값은 `1.0`, 전달된 값이 있으면 해당 값으로 대체.

---

## 4. **`ENV`**
- **설명**: 컨테이너에서 사용할 **환경 변수**를 설정합니다. 빌드 시점뿐만 아니라 컨테이너 실행 중에도 유효.
- **형식**:
  ```dockerfile
  ENV <변수_이름>=<값>
  ```
- **예제**:
  ```dockerfile
  ENV PORT=8080
  ```
  - 컨테이너 내부에서 `$PORT`로 `8080` 참조 가능.

---

## 5. **`WORKDIR`**
- **설명**: 작업 디렉토리를 설정합니다. 이후 명령(`COPY`, `RUN`, 등)은 이 디렉토리를 기준으로 실행됩니다.
- **형식**:
  ```dockerfile
  WORKDIR <경로>
  ```
- **예제**:
  ```dockerfile
  WORKDIR /app
  ```
  - `/app` 디렉토리를 기본 작업 디렉토리로 설정.

---

## 6. **`COPY`**
- **설명**: 파일을 호스트에서 컨테이너로 복사합니다.
- **형식**:
  ```dockerfile
  COPY <소스 경로> <대상 경로>
  ```
- **예제**:
  ```dockerfile
  COPY . /app
  ```
  - 현재 디렉토리(`.`)의 모든 파일을 컨테이너의 `/app` 디렉토리에 복사.

---

## 7. **`ADD`**
- **설명**: `COPY`와 유사하지만 추가 기능으로 **압축 파일 자동 해제** 및 **URL 파일 다운로드** 지원.
- **형식**:
  ```dockerfile
  ADD <소스 경로> <대상 경로>
  ```
- **예제**:
  ```dockerfile
  ADD myapp.tar.gz /app
  ```
  - `myapp.tar.gz`를 컨테이너의 `/app` 디렉토리에 압축 해제.

---

## 8. **`RUN`**
- **설명**: 컨테이너 내부에서 명령어를 실행합니다. 주로 패키지 설치나 빌드 작업에 사용.
- **형식**:
  ```dockerfile
  RUN <명령어>
  ```
- **예제**:
  ```dockerfile
  RUN apt-get update && apt-get install -y curl
  ```
  - `apt-get`을 통해 패키지 설치.

---

## 9. **`CMD`**
- **설명**: 컨테이너가 시작될 때 실행할 **기본 명령어**를 정의합니다. 단일 컨테이너에 하나의 `CMD`만 존재할 수 있습니다.
- **형식**:
  ```dockerfile
  CMD ["실행 파일", "옵션1", "옵션2"]
  ```
- **예제**:
  ```dockerfile
  CMD ["npm", "start"]
  ```
  - 컨테이너 시작 시 `npm start` 실행.

---

## 10. **`ENTRYPOINT`**
- **설명**: 컨테이너의 **고정 실행 파일**을 정의합니다. `CMD`와 조합하여 사용 가능.
- **형식**:
  ```dockerfile
  ENTRYPOINT ["실행 파일", "옵션"]
  ```
- **예제**:
  ```dockerfile
  ENTRYPOINT ["java", "-jar", "app.jar"]
  ```
  - 컨테이너 시작 시 항상 `java -jar app.jar` 실행.

---

## 11. **`EXPOSE`**
- **설명**: 컨테이너에서 사용할 포트를 선언합니다. 이 포트는 컨테이너 외부로 노출되지 않으며, 이를 위해 `-p` 옵션을 사용해야 합니다.
- **형식**:
  ```dockerfile
  EXPOSE <포트 번호>
  ```
- **예제**:
  ```dockerfile
  EXPOSE 8080
  ```

---

## 12. **`VOLUME`**
- **설명**: 컨테이너와 호스트 간에 공유할 디렉토리를 정의합니다.
- **형식**:
  ```dockerfile
  VOLUME ["<경로>"]
  ```
- **예제**:
  ```dockerfile
  VOLUME ["/data"]
  ```
  - 컨테이너의 `/data` 디렉토리를 호스트와 공유.

---

## 13. **`USER`**
- **설명**: 컨테이너 내에서 실행할 사용자와 그룹을 지정합니다.
- **형식**:
  ```dockerfile
  USER <사용자>[:<그룹>]
  ```
- **예제**:
  ```dockerfile
  USER 1001
  ```
  - ID `1001` 사용자로 실행.

---

## 14. **`HEALTHCHECK`**
- **설명**: 컨테이너의 상태를 확인하는 명령을 설정합니다.
- **형식**:
  ```dockerfile
  HEALTHCHECK [옵션] CMD <명령어>
  ```
- **예제**:
  ```dockerfile
  HEALTHCHECK --interval=30s --timeout=10s --retries=3 CMD curl -f http://localhost:8080 || exit 1
  ```

---

## 15. **`ONBUILD`**
- **설명**: 부모 이미지에서 트리거될 명령을 설정합니다.
- **형식**:
  ```dockerfile
  ONBUILD <명령어>
  ```
- **예제**:
  ```dockerfile
  ONBUILD COPY . /app
  ```

