## property 빈 사용하기
- 프로퍼티에 지정된 데이터를 자바 코드에서 읽어오는 방법중에 이를 프로퍼티 빈으로 등록하여 읽어오는 방법
  
### 가장 기본적으로 프로퍼티 정보 읽어오는 방법 
- text를 직접 입력하는 방식이라 오타가 발생할 수도 있고,
- 테스트에서 사용하기도 쉽지 않고, 안이쁘다.
```java
@Configuration(proxyBeanMethods = false)
public class MultipartConfig {

    @Value("${file.multipart.maxUploadSize:20485760}")
    private long maxUploadSize;

    @Value("${file.multipart.maxUploadSizePerFile:20485760}")
    private long maxUploadSizePerFile;
    // ..
}
```
