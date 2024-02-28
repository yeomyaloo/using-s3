# using-s3
- 스프링부트 프로젝트로 파일 업로드 엔드 포인트를 만들어서 해당 파일을 S3에 업로드 해보기

# ⛏ Architecture
![image](https://github.com/yeomyaloo/using-s3/assets/81970382/67366d36-63d7-4117-9388-58572e497882)

# ✏Process
## 1. S3 버킷 생성
![image](https://github.com/yeomyaloo/using-s3/assets/81970382/e2280fbe-1a06-420b-9fa2-a2df245771c9)
![image](https://github.com/yeomyaloo/using-s3/assets/81970382/2b6b0ae9-bc5a-417d-b568-f662dd2e5628)

## 2. Springboot
### 2-1. Configuration
```java
package com.example.demo.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import com.amazonaws.auth.AWSStaticCredentialsProvider;
import com.amazonaws.auth.BasicAWSCredentials;
import com.amazonaws.services.s3.AmazonS3Client;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;

@Configuration
public class S3Config {


	  String accessKey = "AWS_계정에서_발급받은_AccessKey";


    String secretKey = "AWS_계정에서_발급받은_SecretKey";


    String region = "ap-northeast-2"; // 지역 설정

    @Bean
    public AmazonS3Client amazonS3Client() {
        BasicAWSCredentials credentials = new BasicAWSCredentials(accessKey, secretKey);

        return (AmazonS3Client) AmazonS3ClientBuilder
                .standard()
                .withRegion(region)
                .withCredentials(new AWSStaticCredentialsProvider(credentials))
                .build();
    }
}
```

### 2-2. Rest Controller
```java
package com.example.demo.controller;

import java.io.IOException;

import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.model.ObjectMetadata;

import jakarta.servlet.http.HttpServletRequest;
import lombok.RequiredArgsConstructor;


@RestController
@RequestMapping("/upload")
@RequiredArgsConstructor
public class FileUploadController {

	private final AmazonS3 amazonS3;
	
	private String bucket = "본인이_생성한_S3_버킷이름";

	
    @PostMapping
    public void streamUpload(HttpServletRequest request) throws IOException {
        ObjectMetadata metadata = new ObjectMetadata();
        metadata.setContentType(request.getContentType());
        metadata.setContentLength(request.getContentLengthLong());

        amazonS3.putObject(bucket, "objectKey", request.getInputStream(), metadata);
    }
}

```

## 3. postman을 이용한 파일 업로드
![image](https://github.com/yeomyaloo/using-s3/assets/81970382/4cf0c253-53be-4b05-8d78-dbe2aaeaa4a7)

- 포스트맨을 이용해서 해당 파일을 업로드 했다면 이제 S3로 가서 해당 파일이 잘 올라왔는지 확인해주면 된다.

![image](https://github.com/yeomyaloo/using-s3/assets/81970382/d61c11bd-d15e-4b6d-ad47-e7527c0b2826)


