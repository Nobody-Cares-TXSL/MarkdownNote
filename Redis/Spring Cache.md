# Maven坐标

```javascript
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```



```javascript
// 开启缓存注解功能
@EnableCaching
```

# @Cacheable 方法结果缓存

**功能**：主要用于**方法结果缓存**。在方法执行前，先检查缓存中是否存在对应数据，若存在则直接返回缓存数据；若不存在，则执行方法并将结果存入缓存。

**常用属性**：

* `value`/`cacheNames`：指定缓存名称（必填）

* `key`：缓存的键，可以使用 SpEL 表达式自定义

* `condition`：缓存条件，只有满足条件的结果才会被缓存

* `unless`：排除条件，满足条件的结果不会被缓存

# @CachePut 更新缓存

**功能**：主要用于**更新缓存**。无论缓存中是否存在数据，都会执行方法，并将方法返回值更新到缓存中。通常用于数据更新操作。

**常用属性**：

* 与 `@Cacheable` 相同，但没有 `unless` 属性

# @CacheEvict 清除缓存

**功能**：主要用于**清除缓存**。可以删除一条或多条缓存数据，通常用于数据删除或更新时清理相关缓存。

**常用属性**：

* `value`/`cacheNames`：指定缓存名称（必填）

* `key`：要删除的缓存键

* `allEntries`：是否删除缓存中的所有数据，默认为 false

* `beforeInvocation`：是否在方法执行前删除缓存，默认为 false（方法执行后删除）
