# Maven坐标

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>${pagehelper}</version>
</dependency>
```

```java
PageHelper.startPage(employeePageQueryDTO.getPage(), employeePageQueryDTO.getPageSize());
Page<Employee> page = employeeMapper.pageQuery(employeePageQueryDTO);    
long total = page.getTotal();
List<Employee> records = page.getResult();
```

