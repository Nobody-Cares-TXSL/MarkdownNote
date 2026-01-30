# Maven
- [Maven官网下载](https://maven.apache.org/)
- [依赖搜索地址](https://mvnrepository.com/),此链接指向专业的 Maven 仓库搜索平台，可用于查找项目所需的各类依赖。
## Maven 配置
### 配置环境变量
1. 下载解压后添加到环境变量中  

![img](https://img2024.cnblogs.com/blog/3471133/202601/3471133-20260130205808461-1054197445.png)

2. 增加Path变量  

![img](https://img2024.cnblogs.com/blog/3471133/202601/3471133-20260130205922662-1556770289.png)

3. 测试环境变量是否添加成功

![img](https://img2024.cnblogs.com/blog/3471133/202601/3471133-20260130205957797-971510251.png)

### 配置文件
1. 在maven文件下新增mvn_repo文件夹

![img](https://img2024.cnblogs.com/blog/3471133/202601/3471133-20260130210111341-413638396.png)

2. 修改配置conf文件夹下的settings.xml文件
- 添加本地仓库路径
> 路径为: 刚才新建的mvn_repo文件夹路径

![img](https://img2024.cnblogs.com/blog/3471133/202601/3471133-20260130210125067-916722829.png)

- 在mirrors标签下添加阿里云仓库  

![img](https://img2024.cnblogs.com/blog/3471133/202601/3471133-20260130210134899-621704589.png)

### 在IDEA中配置maven构建环境
1. 配置maven环境

![img](https://img2024.cnblogs.com/blog/3471133/202601/3471133-20260130210213043-47248691.png)

2. 在运行程序配置JRE

![img](https://img2024.cnblogs.com/blog/3471133/202601/3471133-20260130210221554-1240004630.png)

3. 项目结构中配置SDK

![img](https://img2024.cnblogs.com/blog/3471133/202601/3471133-20260130210229326-956839048.png)

## maven基础
### 依赖配置
```xml
<!-- pom.xml文件中配置依赖项 -->
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!-- 项目信息 -->
    <groupId>org.example</groupId>
    <artifactId>maven-project01</artifactId>
    <version>1.0-SNAPSHOT</version>

    <!-- 项目属性 -->
    <properties>
        <!-- 编译源代码的JDK版本 -->
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <!-- 编译目标代码的JDK版本 -->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <!-- 依赖项 -->
    <dependencies>
        <!-- 依赖项1 -->
        <dependency>
            <!-- 依赖项的组ID -->
            <groupId>org.springframework</groupId>
            <!-- 依赖项的ID -->
            <artifactId>spring-context</artifactId>
            <!-- 依赖项的版本 -->
            <version>6.1.4</version>

            <!-- 排除依赖 -->
            <exclusions>
                <exclusion>
                    <!-- 排除的依赖项的组ID -->
                    <groupId>io.micrometer</groupId>
                    <!-- 排除的依赖项的ID -->
                    <artifactId>micrometer-observation</artifactId>
                    <!-- 无需指定版本 -->
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>5.9.1</version>
            <!-- 依赖范围 -->
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```
#### 依赖范围
- 可以通过`scope`标签设置依赖范围来控制依赖包的作用范围。

![img](https://img2024.cnblogs.com/blog/3471133/202601/3471133-20260130210254510-155828861.png)

### maven生命周期
Maven有三种生命周期：
1. clean：清理项目
2. default：默认生命周期
3. site：生成站点文档
#### 常用的生命周期阶段
- clean：移除上次的构建文件
- compile：编译源代码
- test：编译测试代码
- package：打包项目
- install：安装项目
> 注意: 在**同一套**生命周期中, 当运行后面的阶段时, 会自动运行前面的阶段  
> **clean是clean生命周期**   
> **其他的是default生命周期**  
## Junit测试
> 需在pom.xml中添加Junit依赖
- **Junit单元测试类名命名规范为: XXXTest** 
- **Junit单元测试方法命名规范为: testXXX** , **方法上增加`@Test`注解**, **必须声明为`public void`**
### 常用注解

![img](https://img2024.cnblogs.com/blog/3471133/202601/3471133-20260130210554438-354019928.jpg)

### 断言

![img](https://img2024.cnblogs.com/blog/3471133/202601/3471133-20260130210612650-1680802796.jpg)

## 继承
+ 子工程继承父工程的依赖

```maven
<!-- 继承父工程 -->
<parent>
    <groupId></groupId>
    <artifactId></artifactId>
    <version></version>
    <relativePath/> <!-- lookup parent from repository -->
    <relativePath>指定父工程的pom.xml文件路径</relativePath>
</parent>

<!-- 定义打包类型 -->
<!-- jar默认打包 war普通web打包 pom聚合工程,仅进行依赖管理 -->
<packaging></packaging>

<!-- 属性配置 -->
<properties>
    <lombok.version>1.18.28</lombok.version>
</properties>
<!-- 引入属性 -->
<version>${lombok.version}</version>

<!-- 依赖管理 -->
<!-- 父工程进行统一依赖管理, 子工程不用指定版本号,但还是需要指定依赖的坐标 -->
<dependencyManagement>
    <dependencies>
        <dependency>
            
        </dependency>
    </dependencies>
</dependencyManagement>

```
## 聚合
+ 聚合工程：不具有业务功能(有且仅有一个pom文件)
```maven
<modules>
    <module>maven-project01</module>
    <module>maven-project02</module>
</modules>
```