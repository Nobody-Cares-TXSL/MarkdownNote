1. 控制反转(IoC)：对象的创建和管理由Spring容器控制，而不是由应用程序代码直接控制

2. 依赖注入(DI)：通过XML配置文件，Spring容器将依赖关系注入到Bean中

3. Bean的生命周期管理：Spring容器负责Bean的创建、初始化、配置和销毁

4. 配置元数据：使用XML文件作为配置元数据，指导Spring容器如何创建和装配Bean

```javascript
public interface IOCInterface {
    void print();
}

public class TeacherImpl implements IOCInterface {
    private String name;
    private int age;
    private LocalDateTime birthday;

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void setBirthday(LocalDateTime birthday) {
        this.birthday = birthday;
    }

    @Override
    public void print() {
        System.out.println("TeacherImpl");
    }

    @Override
    public String toString() {
        return "TeacherImpl{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", birthday=" + birthday +
                '}';
    }
}

public class StudentImpl implements IOCInterface {
    private String name;
    private int age;
    private LocalDateTime birthday;

    public StudentImpl(String name, int age, LocalDateTime birthday) {
        this.name = name;
        this.age = age;
        this.birthday = birthday;
    }

    @Override
    public void print() {
        System.out.println("StudentImpl");
    }

    @Override
    public String toString() {
        return "StudentImpl{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", birthday=" + birthday +
                '}';
    }
}
```

# XML配置文件

Spring容器将依赖关系注入到Bean中,并配置元数据

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- set方法注入 -->
    <bean id="teacherImpl" class="com.nobody_cares.IOC.Impl.TeacherImpl">
        <!-- set方法注入 -->
        <property name="name" value="teacher"></property>
        <property name="age" value="30"></property>
        <property name="birthday" ref="birthday"></property>
    </bean>
    <!-- 构造方法注入 -->
    <bean id="studentImpl" class="com.nobody_cares.IOC.Impl.StudentImpl">
        <!-- 构造方法注入 -->
        <constructor-arg name="name" value="student"></constructor-arg>
        <constructor-arg name="age" value="18"></constructor-arg>
        <!-- ref 引用其他配置文件对象 -->
        <constructor-arg name="birthday" ref="birthday"></constructor-arg>
    </bean>
    <!-- 使用了factory-method="of"来调用LocalDateTime类的静态工厂方法of() -->
    <bean id="birthday" class="java.time.LocalDateTime" factory-method="of">
        <constructor-arg index="0" value="2025"/>
        <constructor-arg index="1" value="10"/>
        <constructor-arg index="2" value="20"/>
        <constructor-arg index="3" value="10"/>
        <constructor-arg index="4" value="0"/>
    </bean>

</beans>
```

# Spring IoC容器的基本功能

```go
// 加载配置文件 创建IOC容器
ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("Bean/bean.xml");
// 获取IOC容器中的对象
StudentImpl studentImpl = classPathXmlApplicationContext.getBean("studentImpl", StudentImpl.class);
TeacherImpl teacherImpl = classPathXmlApplicationContext.getBean("teacherImpl", TeacherImpl.class);
studentImpl.print();
System.out.println(studentImpl);
teacherImpl.print();
System.out.println(teacherImpl);

-----------------------------------------------------------------------------------------------------------------------------
StudentImpl
StudentImpl{name='student', age=18, birthday=2025-10-20T10:00}
TeacherImpl
TeacherImpl{name='teacher', age=30, birthday=2025-10-20T10:00}
```
