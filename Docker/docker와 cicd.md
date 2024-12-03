### **도커(Docker)와 CI/CD**

**도커(Docker)**는 CI/CD(Continuous Integration/Continuous Deployment or Delivery)의 중요한 구성 요소로 활용됩니다.  
도커는 **컨테이너 기반의 환경 격리**와 **이식성**을 제공하여, CI/CD 파이프라인을 간소화하고 배포 프로세스를 효율적으로 만듭니다.

---

## **1. CI/CD란?**

### 1.1 CI(Continuous Integration)
- **지속적 통합**:
  - 개발자가 작성한 코드를 지속적으로 통합 및 테스트.
  - 코드 변경 사항을 병합할 때마다 자동으로 빌드 및 테스트 실행.
- **목적**:
  - 변경 사항이 기존 코드와 충돌하지 않도록 보장.
  - 문제를 빠르게 식별하고 해결.

---

### 1.2 CD(Continuous Deployment/Delivery)
- **지속적 배포(Deployment)**:
  - 새로운 코드를 자동으로 프로덕션 환경에 배포.
  - 완전 자동화된 배포 과정.
- **지속적 전달(Delivery)**:
  - 새로운 코드를 프로덕션에 배포할 준비까지 자동화.
  - 배포는 수동으로 승인.

---

### 1.3 CI/CD의 주요 단계
1. **코드 변경**:
   - 개발자가 새로운 코드를 푸시.
2. **빌드(Build)**:
   - 코드와 의존성을 통합하여 실행 가능한 형태로 만듦.
3. **테스트(Test)**:
   - 자동화된 테스트를 실행하여 문제 탐지.
4. **배포(Deploy)**:
   - 프로덕션 환경으로 배포.

---

## **2. 도커가 CI/CD에 적합한 이유**

도커는 **CI/CD 파이프라인**에서 다음과 같은 문제를 해결합니다:
1. **환경 격리**:
   - "내 컴퓨터에서는 작동하는데..." 문제를 방지.
   - 동일한 컨테이너 이미지를 개발, 테스트, 프로덕션 환경에서 실행 가능.

2. **일관성**:
   - 애플리케이션의 모든 의존성을 하나의 컨테이너 이미지로 패키징.
   - 모든 환경에서 동일하게 작동.

3. **속도**:
   - 도커 이미지 레이어 캐싱으로 빠른 빌드 및 배포 지원.

4. **확장성**:
   - 여러 컨테이너를 동시에 실행하여 병렬 작업 수행 가능.

---

## **3. 도커와 CI/CD 통합**

### 3.1 도커를 사용한 CI/CD 흐름
1. **개발자가 코드 푸시**:
   - Git, SVN 등의 버전 관리 시스템에 코드 저장.

2. **CI 도구가 트리거**:
   - Jenkins, GitLab CI/CD, GitHub Actions 등이 코드 푸시를 감지하여 빌드 프로세스를 시작.

3. **Docker 이미지 빌드**:
   - Dockerfile을 기반으로 애플리케이션 컨테이너 이미지를 생성.

4. **테스트 컨테이너 실행**:
   - 생성된 컨테이너에서 테스트 실행.

5. **이미지 푸시**:
   - 빌드된 이미지를 Docker Hub, AWS ECR, GCP Container Registry 등에 업로드.

6. **배포**:
   - CI/CD 도구가 배포 스크립트를 실행하여 새로운 이미지를 프로덕션에 배포.

---

### 3.2 예제: Jenkins와 Docker
#### **Jenkins Pipeline 예제**
```groovy
pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("myapp:${env.BUILD_ID}")
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    docker.image("myapp:${env.BUILD_ID}").inside {
                        sh 'pytest tests/'
                    }
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        docker.image("myapp:${env.BUILD_ID}").push('latest')
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
            }
        }
    }
}
```
- **Clone**: 소스 코드 가져오기.
- **Build Docker Image**: Dockerfile로 이미지를 빌드.
- **Run Tests**: 생성된 컨테이너에서 테스트 실행.
- **Push Image**: Docker Hub에 이미지 푸시.
- **Deploy**: Kubernetes로 배포.

---

## **4. CI/CD 도구와 도커**

### 4.1 GitHub Actions
- **Docker와 통합된 워크플로우 예제**:
```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Build Docker image
      run: docker build -t myapp .

    - name: Run tests
      run: docker run myapp pytest tests/

    - name: Push to Docker Hub
      env:
        DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
        DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
      run: |
        echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
        docker tag myapp myrepo/myapp:latest
        docker push myrepo/myapp:latest

    - name: Deploy to Kubernetes
      run: kubectl apply -f k8s/deployment.yaml
```

---

### 4.2 GitLab CI/CD
- **GitLab CI/CD에서 Docker 사용**:
```yaml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - docker build -t myrepo/myapp:$CI_COMMIT_SHORT_SHA .

test:
  stage: test
  script:
    - docker run myrepo/myapp:$CI_COMMIT_SHORT_SHA pytest tests/

deploy:
  stage: deploy
  script:
    - docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
    - docker push myrepo/myapp:$CI_COMMIT_SHORT_SHA
    - kubectl apply -f k8s/deployment.yaml
```

---

## **5. 도커와 CI/CD의 장점**

1. **환경 일관성**:
   - 모든 환경에서 동일한 컨테이너 이미지 실행.

2. **빠른 배포**:
   - 이미지 레이어 캐싱을 통해 빌드 및 배포 시간 단축.

3. **효율적인 자원 관리**:
   - 컨테이너 기반으로 빌드 및 테스트 병렬 실행.

4. **이식성**:
   - 어떤 플랫폼에서도 동일한 파이프라인 사용 가능.

5. **확장성**:
   - Kubernetes와 같은 오케스트레이션 도구와 쉽게 통합.

---

## **6. 도커와 CI/CD의 단점**

1. **복잡성 증가**:
   - 도커와 CI/CD 설정 및 관리가 초기에는 까다로울 수 있음.

2. **이미지 크기**:
   - 최적화되지 않은 Dockerfile은 불필요한 크기를 초래.

3. **보안**:
   - 민감한 정보(API 키, 비밀번호 등)가 잘못 노출되면 위험.

---

## **7. 결론**

도커는 CI/CD 환경에서 **빌드, 테스트, 배포의 일관성과 효율성**을 제공하며, 현대 DevOps 워크플로우의 핵심 구성 요소로 자리 잡았습니다.  
CI/CD 도구(GitHub Actions, Jenkins, GitLab 등)와 결합하여 도커의 잠재력을 극대화할 수 있습니다.  

추가 질문이 있으면 언제든 말씀하세요! 😊