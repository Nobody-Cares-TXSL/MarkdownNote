```properties
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>${aliyun.sdk.oss}</version>
</dependency>
```

```yaml
sky:
  alioss:
    # 阿里云OSS的配置信息
    endpoint: ${sky.alioss.endpoint}
    access-key-id: ${sky.alioss.access-key-id}
    access-key-secret: ${sky.alioss.access-key-secret}
    bucket-name: ${sky.alioss.bucket-name}
    
alioss:
  endpoint: oss-cn-beijing.aliyuncs.com
  access-key-id: your-access-key-id  # 实际使用时替换为真实值，不要提交到版本控制系统
  access-key-secret: your-access-key-secret  # 实际使用时替换为真实值，不要提交到版本控制系统
  bucket-name: java-nobodycares
```

配置绑定类

```java
@Component
// @ConfigurationProperties 注解用于将配置文件（如application.yml、application-dev.yml等）中的配置项自动绑定到Java类的属性上
// prefix = "sky.alioss" 指定了配置项的前缀，意味着Spring Boot会自动查找配置文件中以sky.alioss开头的配置项
@ConfigurationProperties(prefix = "sky.alioss")
@Data
public class AliOssProperties {

    private String endpoint;
    private String accessKeyId;
    private String accessKeySecret;
    private String bucketName;

}
```

OssConfiguration 利用配置创建 AliOssUtil，交给 Spring 容器管理

```java
@Slf4j
@Configuration
public class OssConfiguration {
    @Bean
    // 表示如果已有相同 Bean 则不再创建
    @ConditionalOnMissingBean
    public AliOssUtil aliOssUtil(AliOssProperties aliOssProperties){
        log.info("开始创建阿里云文件上传工具类对象：{}",aliOssProperties);
        return new AliOssUtil(aliOssProperties.getEndpoint(),
                aliOssProperties.getAccessKeyId(),
                aliOssProperties.getAccessKeySecret(),
                aliOssProperties.getBucketName());
    }
}
```

```java
@Data
@AllArgsConstructor
@Slf4j
public class AliOssUtil {

    private String endpoint;
    private String accessKeyId;
    private String accessKeySecret;
    private String bucketName;

    /**
     * 文件上传
     *
     * @param bytes
     * @param objectName
     * @return
     */
    public String upload(byte[] bytes, String objectName) {

        // 创建OSSClient实例。
        OSS ossClient = new OSSClientBuilder().build(endpoint, accessKeyId, accessKeySecret);

        try {
            // 创建PutObject请求。
            ossClient.putObject(bucketName, objectName, new ByteArrayInputStream(bytes));
        } catch (OSSException oe) {
            System.out.println("Caught an OSSException, which means your request made it to OSS, "
                    + "but was rejected with an error response for some reason.");
            System.out.println("Error Message:" + oe.getErrorMessage());
            System.out.println("Error Code:" + oe.getErrorCode());
            System.out.println("Request ID:" + oe.getRequestId());
            System.out.println("Host ID:" + oe.getHostId());
        } catch (ClientException ce) {
            System.out.println("Caught an ClientException, which means the client encountered "
                    + "a serious internal problem while trying to communicate with OSS, "
                    + "such as not being able to access the network.");
            System.out.println("Error Message:" + ce.getMessage());
        } finally {
            if (ossClient != null) {
                // 关闭OSSClient。
                ossClient.shutdown();
            }
        }

        //文件访问路径规则 https://BucketName.Endpoint/ObjectName
        StringBuilder stringBuilder = new StringBuilder("https://");
        stringBuilder
                .append(bucketName)
                .append(".")
                .append(endpoint)
                .append("/")
                .append(objectName);

        log.info("文件上传到:{}", stringBuilder.toString());

        return stringBuilder.toString();
    }
}
```

```java
@PostMapping("/upload")
@ApiOperation(value = "文件上传")
public Result<String> upload(@RequestBody MultipartFile file) {
    log.info("文件上传：{}", file);

    try {
        // 获取原始文件名
        String originalFilename = file.getOriginalFilename();
        // 截取文件后缀
        String extension = originalFilename.substring(originalFilename.lastIndexOf("."));
        String objectName = UUID.randomUUID().toString() + extension;

        String filePath = aliOssUtil.upload(file.getBytes(), objectName);
        return Result.success(filePath);
    } catch (IOException e) {
        log.error("文件上传失败：{}", e.getMessage());
    }
    return Result.error(MessageConstant.UPLOAD_FAILED);
}
```

