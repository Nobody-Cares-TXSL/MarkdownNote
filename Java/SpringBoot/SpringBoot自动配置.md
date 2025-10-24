# SpringBoot自动配置
> 自动配置模块的核心，就是编写自动配置的核心代码，然后将自动配置的核心类，配置在核心的配置文件 
 
配置文件: `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`  

## 阿里云OSS自动配置

- 第1步：创建自定义starter模块 aliyun-oss-spring-boot-starter（进行依赖管理）
  - 把阿里云OSS所有的依赖统一管理起来
- 第2步：创建autoconfigure模块 aliyun-oss-spring-boot-autoconfigure
  - 在starter中引入autoconfigure （我们使用时只需要引入starter起步依赖即可）
- 第3步：在autoconfigure模块aliyun-oss-spring-boot-autoconfigure中完成自动配置
  1. 定义一个自动配置类，在自动配置类中将所要配置的bean都提前配置好
  2. 定义配置文件，把自动配置类的全类名定义在配置文件(META-INF/spring/xxxx.imports)中

![3968X1684/SpringBoot自动配置.png](https://tc-new.z.wiki/autoupload/f/ysNJy6oaVi9q7Gdzj9MLEwltnIuRGTHBNTzJvC5G0G2yl5f0KlZfm6UsKj-HyTuv/20250819/IJa7/3968X1684/SpringBoot%E8%87%AA%E5%8A%A8%E9%85%8D%E7%BD%AE.png)