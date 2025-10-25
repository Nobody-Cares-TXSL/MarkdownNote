# Maven坐标

```xml
<dependency> 
    <groupId>io.jsonwebtoken</groupId> 
    <artifactId>jjwt</artifactId> 
    <version>${jjwt}</version> 
</dependency>
```

# 生成jwt

* &#x20;指定签名的时候使用的签名算法

* &#x20; SignatureAlgorithm signatureAlgorithm = SignatureAlgorithm.*HS256*;

1. 构建 JWT：使用 Jwts.builder() 创建一个 JWT 构建器对象，并进行三项核心设置：

   * 设置自定义声明：`.setClaims(claims)` 将用户提供的自定义信息添加到令牌中

   * 设置签名信息：`.signWith(signatureAlgorithm, secretKey.getBytes(StandardCharsets.UTF_8))` 使用指定算法和密钥生成签名

   编码处理：密钥在使用前通过 `getBytes(StandardCharsets.UTF_8)` 转换为 UTF-8 编码的字节数组，确保跨平台一致性。&#x20;

   * 设置过期时间：`.setExpiration(exp)` 设置令牌的有效期

2. 生成最终令牌：通过 `builder.compact()` 方法将构建的 JWT 对象序列化为最终的字符串形式。

```typescript
/**
 * 生成jwt
 * 使用Hs256算法, 私匙使用固定秘钥
 *
 * @param secretKey jwt秘钥
 * @param ttlMillis jwt过期时间(毫秒)
 * @param claims    设置的信息
 * @return
 */
public static String createJWT(String secretKey, long ttlMillis, Map<String, Object> claims) {
    // 指定签名的时候使用的签名算法，也就是header那部分
    SignatureAlgorithm signatureAlgorithm = SignatureAlgorithm.HS256;

    // 生成JWT的时间
    long expMillis = System.currentTimeMillis() + ttlMillis;
    Date exp = new Date(expMillis);

    // 设置jwt的body
    JwtBuilder builder = Jwts.builder()
            // 如果有私有声明，一定要先设置这个自己创建的私有的声明，这个是给builder的claim赋值，一旦写在标准的声明赋值之后，就是覆盖了那些标准的声明的
            .setClaims(claims)
            // 设置签名使用的签名算法和签名使用的秘钥
            .signWith(signatureAlgorithm, secretKey.getBytes(StandardCharsets.UTF_8))
            // 设置过期时间
            .setExpiration(exp);

    return builder.compact();
}
```

# 解析jwt

1. 创建解析器: 通过 `Jwts.parser()` 创建一个 JWT 解析器实例

2. 设置验证密钥: 使用 `setSigningKey()` 方法设置用于验证签名的密钥，这里同样将密钥转换为 UTF-8 编码的字节数组

3. 解析并验证令牌: 调用 `parseClaimsJws(token)` 方法解析令牌，这个方法会自动验证：

   * 令牌的签名是否有效（使用提供的密钥）

   * 令牌是否已过期

   * 令牌的格式是否正确

4. 获取声明信息: 通过 `getBody()` 方法获取令牌中包含的所有声明信息

```typescript
/**
 * Token解密
 *
 * @param secretKey jwt秘钥 此秘钥一定要保留好在服务端, 不能暴露出去, 否则sign就可以被伪造, 如果对接多个客户端建议改造成多个
 * @param token     加密后的token
 * @return
 */
public static Claims parseJWT(String secretKey, String token) {
    // 得到DefaultJwtParser
    Claims claims = Jwts.parser()
            // 设置签名的秘钥
            .setSigningKey(secretKey.getBytes(StandardCharsets.UTF_8))
            // 设置需要解析的jwt
            .parseClaimsJws(token).getBody();
    return claims;
}
```

# 映射配置

```yaml
sky:
  jwt:
    # 设置jwt签名加密时使用的秘钥
    admin-secret-key: itcast
    # 设置jwt过期时间
    admin-ttl: 7200000
    # 设置前端传递过来的令牌名称
    admin-token-name: token
    # 登录用户jwt的配置信息
    user-secret-key: itcast
    user-ttl: 7200000
    # 设置前端传递过来的令牌名称
    user-token-name: authentication
```

创建一个JwtProperties类进行配置映射

```typescript
@Component
@ConfigurationProperties(prefix = "sky.jwt")
@Data
public class JwtProperties {

    /**
     * 管理端员工生成jwt令牌相关配置
     */
    private String adminSecretKey;
    private long adminTtl;
    private String adminTokenName;

    /**
     * 用户端微信用户生成jwt令牌相关配置
     */
    private String userSecretKey;
    private long userTtl;
    private String userTokenName;

}
```
