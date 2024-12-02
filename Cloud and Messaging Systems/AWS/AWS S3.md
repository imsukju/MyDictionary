### S3 버킷이란?

#### 1. 정의
S3(Simple Storage Service)는 **AWS(Amazon Web Services)**가 제공하는 확장 가능하고 안전한 객체 스토리지 서비스입니다.  
- **버킷**: 데이터를 저장하는 최상위 컨테이너로, 디렉터리 같은 역할을 합니다.
- **객체**: 버킷에 저장되는 데이터 단위(파일, 메타데이터 등).
- **특징**: 무제한 스토리지, 낮은 비용, 고가용성, 높은 내구성을 제공합니다.

#### 2. 주요 특징
1. **확장성**:
   - 용량 제한 없이 데이터를 저장 가능.
   - 스토리지 용량에 따라 비용 부과.

2. **안전성**:
   - 기본적으로 99.999999999% 내구성을 제공.
   - 데이터 암호화(AES-256, KMS) 및 권한 관리(ACL, IAM)를 지원.

3. **접근성**:
   - HTTP/HTTPS 프로토콜을 통해 전 세계 어디서든 접근 가능.
   - 공공/비공개 데이터 설정 가능.

4. **버전 관리**:
   - 같은 이름의 객체가 업데이트될 때 이전 버전을 유지할 수 있는 기능.

5. **용도**:
   - 백업/복구.
   - 정적 웹사이트 호스팅.
   - 로그 및 이벤트 데이터 저장.

---

### S3 버킷 활용 사례 (Java Spring Boot와 연계하기 전에 이해할 기본 개념)

#### 1. 정적 콘텐츠 저장
- 이미지, 동영상, HTML, CSS, JS 파일을 저장하고 사용자에게 제공.

#### 2. 백업 및 아카이빙
- 데이터 백업 및 장기 보관.

#### 3. 데이터 공유
- 다른 애플리케이션 및 사용자와 데이터를 공유.

#### 4. 데이터 처리
- 머신러닝 모델 학습용 데이터 저장소로 활용.

---

### Spring Boot와 AWS S3 통합하기
Spring Boot를 사용하면 애플리케이션에서 S3 버킷과 쉽게 상호작용할 수 있습니다.

---

#### **1. AWS 계정 및 S3 버킷 설정**

1. **AWS 계정 생성**:
   - [AWS 공식 사이트](https://aws.amazon.com/)에서 계정을 만듭니다.

2. **IAM 사용자 생성**:
   - S3 접근 권한을 가진 IAM 사용자를 생성합니다.
   - `AmazonS3FullAccess` 정책을 할당합니다.

3. **버킷 생성**:
   - AWS Management Console에서 S3 버킷 생성.
   - 버킷 이름은 전역적으로 고유해야 함.

4. **액세스 키 발급**:
   - IAM 사용자에서 `액세스 키`와 `비밀 액세스 키`를 발급받습니다.

---

#### **2. Spring Boot 프로젝트 환경 설정**

1. **필요한 의존성 추가**:
   - Maven 기반 프로젝트라면 `pom.xml`에 AWS SDK와 Spring Cloud AWS 의존성을 추가합니다.

```xml
<dependencies>
    <!-- AWS SDK -->
    <dependency>
        <groupId>software.amazon.awssdk</groupId>
        <artifactId>s3</artifactId>
        <version>2.20.0</version> <!-- 최신 버전 확인 -->
    </dependency>
    
    <!-- Spring Cloud AWS -->
    <dependency>
        <groupId>io.awspring.cloud</groupId>
        <artifactId>spring-cloud-starter-aws</artifactId>
        <version>3.0.1</version> <!-- 최신 버전 확인 -->
    </dependency>
</dependencies>
```

2. **`application.yml` 또는 `application.properties` 파일에 AWS 설정 추가**:

```yaml
cloud:
  aws:
    credentials:
      access-key: YOUR_ACCESS_KEY
      secret-key: YOUR_SECRET_KEY
    region:
      static: YOUR_REGION
    s3:
      bucket: YOUR_BUCKET_NAME
```

---

#### **3. S3와 상호작용하는 코드 작성**

1. **S3Client Bean 생성**:

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.s3.S3Client;

@Configuration
public class S3Config {

    @Bean
    public S3Client s3Client() {
        return S3Client.builder()
                .region(Region.US_EAST_1) // 사용할 AWS 리전 설정
                .build();
    }
}
```

2. **파일 업로드 서비스 작성**:

```java
import org.springframework.stereotype.Service;
import org.springframework.web.multipart.MultipartFile;
import software.amazon.awssdk.services.s3.S3Client;
import software.amazon.awssdk.services.s3.model.PutObjectRequest;

import java.io.IOException;
import java.nio.file.Paths;

@Service
public class S3Service {

    private final S3Client s3Client;

    public S3Service(S3Client s3Client) {
        this.s3Client = s3Client;
    }

    public String uploadFile(MultipartFile file, String bucketName) throws IOException {
        String fileName = file.getOriginalFilename();

        // S3 업로드 요청 생성
        PutObjectRequest putObjectRequest = PutObjectRequest.builder()
                .bucket(bucketName)
                .key(fileName)
                .build();

        // 파일 업로드 실행
        s3Client.putObject(putObjectRequest,
                Paths.get(file.getOriginalFilename()));

        return fileName;
    }
}
```

3. **Controller 작성**:

```java
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

@RestController
@RequestMapping("/s3")
public class S3Controller {

    private final S3Service s3Service;

    public S3Controller(S3Service s3Service) {
        this.s3Service = s3Service;
    }

    @PostMapping("/upload")
    public ResponseEntity<String> uploadFile(@RequestParam("file") MultipartFile file) {
        try {
            String fileName = s3Service.uploadFile(file, "YOUR_BUCKET_NAME");
            return ResponseEntity.ok("File uploaded successfully: " + fileName);
        } catch (Exception e) {
            return ResponseEntity.status(500).body("File upload failed: " + e.getMessage());
        }
    }
}
```

---

#### **4. 실행 및 테스트**

1. **Spring Boot 애플리케이션 실행**:
   - 프로젝트를 실행합니다.

2. **API 테스트**:
   - Postman이나 cURL을 사용하여 `POST /s3/upload` API에 파일 업로드 요청.

3. **S3 콘솔 확인**:
   - AWS S3 콘솔에서 파일이 제대로 업로드되었는지 확인.

---

### S3 활용 팁 및 주의사항

1. **비용 관리**:
   - S3에 저장된 데이터와 전송된 데이터는 비용이 발생합니다. 불필요한 데이터는 삭제하세요.

2. **보안**:
   - IAM 정책을 통해 필요한 권한만 부여합니다.
   - 파일 업로드 시 공개 파일 여부를 주의하세요.

3. **로깅**:
   - S3 액세스 로그를 활성화하여 누가 어떤 데이터를 사용했는지 추적합니다.

4. **효율성**:
   - 대용량 파일 업로드 시 멀티파트 업로드를 고려하세요.

이와 같은 방식으로 S3 버킷을 Spring Boot 애플리케이션과 통합하여 효과적으로 활용할 수 있습니다. 추가적인 기능(다운로드, 삭제, 버전 관리 등)이 필요하다면 AWS SDK 문서를 참조하여 확장하면 됩니다.