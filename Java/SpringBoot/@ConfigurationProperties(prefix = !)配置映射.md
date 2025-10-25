`@ConfigurationProperties(prefix = "")` 是 Spring Boot 提供的一个核心注解

用于**将配置文件（如 application.yml 或 application.properties）中的配置项自动绑定到 Java 类的属性上**。



`@ConfigurationProperties` 注解**默认支持驼峰命名（camelCase）与短横线命名（kebab-case）之间的自动转换**，这是 Spring Boot 的内置功能。

### 自动转换的工作原理

Spring Boot 在绑定配置属性时，会自动处理以下几种命名格式之间的转换：

1. 驼峰命名法（camelCase）：如 `adminSecretKey`（Java 属性名）

2. 短横线命名法（kebab-case）：如 `admin-secret-key`（配置文件中使用）

3. 下划线命名法（snake\_case）：如 `admin_secret_key`（也支持）

4. 全大写+下划线：如 `ADMIN_SECRET_KEY`（环境变量中常见）

这种转换是由 Spring Boot 的 `RelaxedBinding` 机制实现的，它会尝试多种可能的命名格式进行匹配。

# 注解中的 `prefix = ""` 指定了配置项的前缀



