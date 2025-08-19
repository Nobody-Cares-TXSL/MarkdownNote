# Maven
- [Maven官网下载](https://maven.apache.org/)
- [依赖搜索地址](https://mvnrepository.com/),此链接指向专业的 Maven 仓库搜索平台，可用于查找项目所需的各类依赖。
## Maven 配置
### 配置环境变量
1. 下载解压后添加到环境变量中
![653X183/Snipaste_2025-07-17_03-03-17.png](https://tc.z.wiki/autoupload/f/XrZ3KSvg-vznRvezsTJQSyU3Jsth-gW-s0TGLnbf7q2yl5f0KlZfm6UsKj-HyTuv/20250717/3QwP/653X183/Snipaste_2025-07-17_03-03-17.png)
2. 增加Path变量  
![581X427/Snipaste_2025-07-17_03-07-00.png](https://tc.z.wiki/autoupload/f/XrZ3KSvg-vznRvezsTJQSyU3Jsth-gW-s0TGLnbf7q2yl5f0KlZfm6UsKj-HyTuv/20250717/HWDG/581X427/Snipaste_2025-07-17_03-07-00.png)
3. 测试环境变量是否添加成功
![960X480/Snipaste_2025-07-17_03-08-57.png](https://tc.z.wiki/autoupload/f/XrZ3KSvg-vznRvezsTJQSyU3Jsth-gW-s0TGLnbf7q2yl5f0KlZfm6UsKj-HyTuv/20250717/1v4G/960X480/Snipaste_2025-07-17_03-08-57.png)
### 配置文件
1. 在maven文件下新增mvn_repo文件夹
![447X301/Snipaste_2025-07-17_03-43-42.png](https://tc.z.wiki/autoupload/f/XrZ3KSvg-vznRvezsTJQSyU3Jsth-gW-s0TGLnbf7q2yl5f0KlZfm6UsKj-HyTuv/20250717/sHK4/447X301/Snipaste_2025-07-17_03-43-42.png)
2. 修改配置conf文件夹下的settings.xml文件
- 添加本地仓库路径
> 路径为: 刚才新建的mvn_repo文件夹路径
![738X139/Snipaste_2025-07-17_03-46-57.png](https://tc.z.wiki/autoupload/f/XrZ3KSvg-vznRvezsTJQSyU3Jsth-gW-s0TGLnbf7q2yl5f0KlZfm6UsKj-HyTuv/20250717/Rokf/738X139/Snipaste_2025-07-17_03-46-57.png)
- 在mirrors标签下添加阿里云仓库  
![615X412/Snipaste_2025-07-17_03-47-24.png](https://tc.z.wiki/autoupload/f/XrZ3KSvg-vznRvezsTJQSyU3Jsth-gW-s0TGLnbf7q2yl5f0KlZfm6UsKj-HyTuv/20250717/mId6/615X412/Snipaste_2025-07-17_03-47-24.png)
### 在IDEA中配置maven构建环境
1. 配置maven环境
![982X734/Snipaste_2025-07-17_03-57-29.png](https://tc.z.wiki/autoupload/f/XrZ3KSvg-vznRvezsTJQSyU3Jsth-gW-s0TGLnbf7q2yl5f0KlZfm6UsKj-HyTuv/20250717/wqqf/982X734/Snipaste_2025-07-17_03-57-29.png)  
2. 在运行程序配置JRE
![982X734/Snipaste_2025-07-17_03-58-28.png](https://tc.z.wiki/autoupload/f/XrZ3KSvg-vznRvezsTJQSyU3Jsth-gW-s0TGLnbf7q2yl5f0KlZfm6UsKj-HyTuv/20250717/Z287/982X734/Snipaste_2025-07-17_03-58-28.png)  
3. 项目结构中配置SDK
![1024X854/Snipaste_2025-07-17_04-01-45.png](https://tc.z.wiki/autoupload/f/XrZ3KSvg-vznRvezsTJQSyU3Jsth-gW-s0TGLnbf7q2yl5f0KlZfm6UsKj-HyTuv/20250717/l9l4/1024X854/Snipaste_2025-07-17_04-01-45.png)  
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
![398X162/Snipaste_2025-07-18_02-13-10.png](https://tc.z.wiki/autoupload/f/XrZ3KSvg-vznRvezsTJQSyU3Jsth-gW-s0TGLnbf7q2yl5f0KlZfm6UsKj-HyTuv/20250718/4cH3/398X162/Snipaste_2025-07-18_02-13-10.png)
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
[![pV39V5d.jpg](https://s21.ax1x.com/2025/07/17/pV39V5d.jpg)](https://imgse.com/i/pV39V5d)  
### 断言
[![pV39ePA.jpg](https://s21.ax1x.com/2025/07/17/pV39ePA.jpg)](https://imgse.com/i/pV39ePA)

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