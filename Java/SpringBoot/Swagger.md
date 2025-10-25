**基于Swagger和Knife4j实现的接口文档自动生成功能**

当 Spring Boot 项目启动时，Swagger/Knife4j 会执行以下操作：

1. 组件扫描：Spring Boot 自动扫描并加载 `Docket` 类型的 Bean

2. 包路径扫描：根据 `Docket` 中配置的 `basePackage`，扫描对应的控制器包

3. 注解解析：解析控制器类和方法上的 Swagger 注解，提取文档信息

4. 文档数据生成：基于扫描和解析的结果，生成 Swagger 规范的 JSON 文档数据

5. UI 资源加载：加载 Knife4j 的 UI 资源，准备展示文档界面



1. 导入 knife4j 的maven坐标在pom.xml中添加依赖

```xml
<dependency>
  <groupId>com.github.xiaoymin</groupId>
  <artifactId>knife4j-spring-boot-starter</artifactId>
</dependency>

这个依赖会自动将其 UI 资源打包到 JAR 文件的 /META-INF/resources/ 目录中
```

* 在配置类中加入 knife4j 相关配置WebMvcConfiguration.java

WebMvcConfiguration extends WebMvcConfigurationSupport

```java
/**
 * 通过knife4j生成接口文档
 * @return
 */
@Bean
public Docket docketAdmin() {
    ApiInfo apiInfo = new ApiInfoBuilder()
            .title("苍穹外卖项目接口文档")
            .version("2.0")
            .description("苍穹外卖项目接口文档")
            .build();
    Docket docket = new Docket(DocumentationType.SWAGGER_2)
            // 设置分组名称，用于区分不同模块的接口文档
            .groupName("管理端接口")
            // 关联前面创建的ApiInfo对象
            .apiInfo(apiInfo)
            // 开始配置接口扫描规则
            .select()
            // 指定要扫描的包路径
            .apis(RequestHandlerSelectors.basePackage("com.sky.controller.admin"))
            // 指定要扫描的路径规则，这里表示所有路径都要扫描
            .paths(PathSelectors.any())
            .build();
    return docket;
}

@Bean
public Docket docketUser() {
    ApiInfo apiInfo = new ApiInfoBuilder()
            .title("苍穹外卖项目接口文档")
            .version("2.0")
            .description("苍穹外卖项目接口文档")
            .build();
    Docket docket = new Docket(DocumentationType.SWAGGER_2)
            .groupName("用户端接口")
            .apiInfo(apiInfo)
            .select()
            .apis(RequestHandlerSelectors.basePackage("com.sky.controller.user"))
            .paths(PathSelectors.any())
            .build();
    return docket;
}
```

* 设置静态资源映射，否则接口文档页面无法访问WebMvcConfiguration.java

**自定义 Spring MVC 的静态资源处理规则**

```typescript
/**
      * 设置静态资源映射
      * @param registry
 */
protected void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/doc.html").addResourceLocations("classpath:/META-INF/resources/");
    registry.addResourceHandler("/webjars/**").addResourceLocations("classpath:/META-INF/resources/webjars/");
}
```

