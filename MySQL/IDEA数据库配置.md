使用阿里巴巴的 Druid 连接池管理数据库连接

```yaml
spring:
  datasource:
    # 使用Druid连接池，而不是Spring Boot默认的HikariCP连接池
    druid:
      driver-class-name: ${sky.datasource.driver-class-name}
      url: jdbc:mysql://${sky.datasource.host}:${sky.datasource.port}/${sky.datasource.database}?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&useSSL=false&allowPublicKeyRetrieval=true
      username: ${sky.datasource.username}
      password: ${sky.datasource.password}
      
sky:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    host: localhost
    port: 3306
    database: sky_take_out
    username: root
    password: 1234
```

### mysql-connector-java

**主要作用**：

* **提供JDBC驱动实现**：作为MySQL数据库的官方JDBC驱动，负责在Java应用程序和MySQL数据库之间建立连接通道

* **支持SQL操作执行**：实现了JDBC接口规范，使得应用程序能够通过标准JDBC API执行SQL语句、处理结果集

* **连接管理支持**：提供了与MySQL数据库通信的底层协议实现，包括连接建立、认证、数据传输等

* **配置适配**：支持application.yml中配置的`spring.datasource.driver-class-name`和`spring.datasource.url`等MySQL相关参数

**作用特点**：

* 被设置为`<scope>runtime</scope>`，表示该依赖仅在运行时需要，不参与编译过程

* 是数据源配置能够正常工作的基础组件，没有此依赖，应用无法连接到MySQL数据库

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

### druid-spring-boot-starter

**主要作用**：

* 数据库连接池实现：提供了高性能、功能丰富的数据库连接池，负责管理数据库连接的创建、复用和释放

* 自动配置支持：作为Spring Boot Starter，能够自动配置Druid连接池，无需手动编写大量配置代码

* 监控功能：内置强大的监控功能，可以监控SQL执行、连接池状态等重要指标

* 安全防御：提供SQL注入防护、密码加密等安全特性

* 配置增强：支持application.yml中以`spring.datasource.druid.*`为前缀的各种连接池配置参数

**与数据源配置的关系**：

* 使得项目能够使用连接池技术，优化数据库连接性能

* 支持配置连接池大小、连接超时、验证查询等参数，实现更精细的连接管理

* 与mysql-connector-java协同工作，前者提供连接池管理，后者提供底层数据库连接能力

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>${druid}</version>
</dependency>
```

