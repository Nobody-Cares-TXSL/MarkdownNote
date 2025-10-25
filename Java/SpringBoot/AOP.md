# 为数据库实现重复字段的自动填充

```java
// 操作类型枚举
public enum OperationType {

    /**
     * 更新操作
     */
    UPDATE,

    /**
     * 插入操作
     */
    INSERT

}
```

```java
/**
 * 自定义注解，用于标识某个方法需要自动填充创建时间和更新时间
 */
@Target(ElementType.METHOD)
// 表示注解保留在运行时
@Retention(RetentionPolicy.RUNTIME)
public @interface AutoFill {
    // 操作类型，默认是插入操作
    OperationType value() default OperationType.INSERT;
}
```

在要自动填充字段的mapper接口上增加定义的注解

```java
/**
 * 新增分类
 *
 * @param category
 */
@AutoFill
void insert(Category category);

/**
 * 修改分类
 *
 * @param category
 */
@AutoFill(OperationType.UPDATE)
void update(Category category);
```

切面类,通过反射完成操作

```java
@Aspect
@Component
@Slf4j
public class AutoFillAspect {
    @Before("@annotation(com.sky.annotation.AutoFill)")
    public void autoFill(JoinPoint joinPoint) throws NoSuchFieldException, IllegalAccessException {
        log.info("autoFill start");
        // 获取参数对象
        Object arg = joinPoint.getArgs()[0];
        // 获取当前方法名
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        // 根据方法名获取方法对象
        Method method = signature.getMethod();
        // 根据方法对象获取注解对象
        AutoFill annotation = method.getAnnotation(AutoFill.class);
        // 获取注解对象中定义的操作类型
        OperationType value = annotation.value();

        if (value == OperationType.INSERT) {
            // 获取参数的类对象
            Class clazz = arg.getClass();
            // 获取属性
            Field createTime = clazz.getDeclaredField("createTime");
            Field updateTime = clazz.getDeclaredField("updateTime");
            Field createUser = clazz.getDeclaredField("createUser");
            Field updateUser = clazz.getDeclaredField("updateUser");
            // 暴力反射
            createTime.setAccessible(true);
            updateTime.setAccessible(true);
            createUser.setAccessible(true);
            updateUser.setAccessible(true);
            // 设置属性值
            createTime.set(arg, LocalDateTime.now());
            updateTime.set(arg, LocalDateTime.now());
            createUser.set(arg, BaseContext.getCurrentId());
            updateUser.set(arg, BaseContext.getCurrentId());
        } else {
            //5.1获取参数的字节码对象
            Class clazz = arg.getClass();
            //5.2获取属性
            Field updateTime = clazz.getDeclaredField("updateTime");
            Field updateUser = clazz.getDeclaredField("updateUser");
            //暴力反射
            updateTime.setAccessible(true);
            updateUser.setAccessible(true);
            //给属性赋值
            updateTime.set(arg,LocalDateTime.now());
            updateUser.set(arg,BaseContext.getCurrentId());
        }
    }
}
```
