**Spring Data Redis中提供了一个高度封装的类：RedisTemplate**

对相关api进行了**归类封装**,将同一类型操作**封装为operation接口**，具体分类如下：

**ValueOperations**：string数据操作

**SetOperations**：set类型数据操作

**ZSetOperations**：zset类型数据操作

**HashOperations**：hash类型的数据操作

**ListOperations**：list类型的数据操作

# 插入maven坐标

```sql
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<!-- Jackson: 默认情况下，Spring Boot 使用 Jackson 来处理 JSON 数据-->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
</dependency>
```

# 配置Redis数据源

```yaml
spring:
  redis:
    host: localhost
    port: 6379 # 端口号
    password: 123456 # Redis密码
    database: 10  #指定使用那个数据库，默认是0号数据库，不同数据库中的数据是完全隔离的
```

# 编写配置类

```javascript
/**
 * RedisTemplate的两种序列化实践方案：
 * 方案一：
 * 自定义RedisTemplate
 * 修改RedisTemplate的序列化器为GenericJackson2JsonRedisSerializer
 * 方案二：
 * 使用StringRedisTemplate
 * 写入Redis时，手动把对象序列化为JSON
 * 读取Redis时，手动把读取到的JSON反序列化为对象
 *
 * @author nobody_cares
 * @date 2023/5/23 17:05
 */
@Configuration
public class RedisConfiguration {


    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        // 配置连接工厂
        template.setConnectionFactory(redisConnectionFactory);

        // 设置Key的序列化
        template.setKeySerializer(new StringRedisSerializer());
        template.setHashKeySerializer(new StringRedisSerializer());

        // 创建JSON序列化器 序列化的java对象应该有无参构造函数
        // 会多一个"@class": "com.nobody_cares.Entity.User"样子的类引用
        GenericJackson2JsonRedisSerializer jsonRedisSerializer = new GenericJackson2JsonRedisSerializer();
        // 设置Value的序列化 - 使用JSON序列化器
        template.setValueSerializer(jsonRedisSerializer);
        template.setHashValueSerializer(jsonRedisSerializer);

        // 初始化RedisTemplate
        template.afterPropertiesSet();

        return template;
    }

    @Bean
    public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
        StringRedisTemplate template = new StringRedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
}
```

# 简单操作

```java
@SpringBootTest
class RedisApplicationTests {
    @Autowired
    private RedisTemplate redisTemplate;
    @Autowired
    private StringRedisTemplate stringRedisTemplate;
    // 创建JSON序列化器
    private static final ObjectMapper objectMapper = new ObjectMapper();

    @Test
    void redisTemplateTest() throws JsonProcessingException {

        // 方式一
        redisTemplate.opsForValue().set("User:name_1", new User("张三", 18));
        System.out.println(redisTemplate.opsForValue().get("User:name_1"));


        // 方式二
        User user = new User("李四", 20);
        // 将对象转为JSON字符串
        String userJson = objectMapper.writeValueAsString(user);

        stringRedisTemplate.opsForValue().set("User:name_2", userJson);
        String userString = stringRedisTemplate.opsForValue().get("User:name_2");

        // 将JSON字符串转为对象
        User UserValue = objectMapper.readValue(userJson, User.class);
        System.out.println(UserValue);
    }
}
```

# 通过Redis模版对象操作Redis

通过IOC容器进行依赖注入

```sql
@AutoWired
private RedisTemplate redisTemplate;
```

## 字符串类型

```javascript
// set、 get 、setex、 setnx（对应之间讲的4个命令）
//1.设置指定key的值
redisTemplate.opsForValue().set("name","小明");
//2. 获取key值, 返回的是Object对象, 可强转
String name = (String) redisTemplate.opsForValue().get("name");
//3. 设置指定key的值，并指定 key 的过期时间
// 参数：k、V、过期时间、单位（TimeUnit.MINUTES分钟，这是枚举类型）
redisTemplate.opsForValue().set("code","1234",3, TimeUnit.MINUTES);
//4.只有在 key 不存在时设置 key 的值
redisTemplate.opsForValue().setIfAbsent("lock","1"); //第一次没有k成功
redisTemplate.opsForValue().setIfAbsent("lock","2"); //第二次有k失败
```

## 哈希类型

```javascript
//hset、 hget、 hdel、 hkeys、 hvals
        HashOperations hashOperations = redisTemplate.opsForHash();

        //1.将哈希表 key 中的字段 field 的值设为 value
        hashOperations.put("100","name","tom");
        hashOperations.put("100","age","20");

        //2.获取存储在哈希表中指定字段的值，返回的是object对象，可以进行强转
        String name = (String) hashOperations.get("100", "name");
        System.out.println(name); //tom

        //3.获取哈希表中所有字段
        Set keys = hashOperations.keys("100");
        System.out.println(keys);//[name, age]

        //4.获取哈希表中所有值
        List values = hashOperations.values("100");
        System.out.println(values);//[tom, 20]

        //5. 删除存储在哈希表中的指定字段
        hashOperations.delete("100","age");
```

## 列表类型

```java
//lpush 、lrange 、rpop 、llen
        ListOperations listOperations = redisTemplate.opsForList();

        //1. 将一个或多个值插入到列表头部
        listOperations.leftPushAll("mylist","a","b","c");//一次性插入多个
        listOperations.leftPush("mylist","d");//一次性插入一个

        //2.获取列表指定范围内的元素  0代表从队列头部开始   -1代表队列的尾部
        List mylist = listOperations.range("mylist", 0, -1);
        System.out.println(mylist);//[d, c, b, a]

        //3.移除并获取列表最后一个元素，并且这个命令会返回这个值
        listOperations.rightPop("mylist");

        //4.获取列表长度
        Long size = listOperations.size("mylist");
        System.out.println(size);//3
```

## 集合类型

```java
//sadd、 smembers 、scard 、sinter、 sunion 、srem
        SetOperations setOperations = redisTemplate.opsForSet();

        //1.向集合添加一个或多个成员
        setOperations.add("set1","a","b","c","d");
        setOperations.add("set2","a","b","x","y");

        //2. 返回集合中的所有成员
        Set members = setOperations.members("set1");
        System.out.println(members);//[d, c, b, a]

        //3.获取集合的成员数
        Long size = setOperations.size("set1");
        System.out.println(size);//4

        //4.返回给定所有集合的交集
        Set intersect = setOperations.intersect("set1", "set2");
        System.out.println(intersect);//[b, a]

        //5.返回所有给定集合的并集
        Set union = setOperations.union("set1", "set2");
        System.out.println(union);//[b, a, d, c, y, x]

        //6.移除集合中一个或多个成员
        setOperations.remove("set1","a","b");
```

## 有序集合类型

```java
//zadd 、zrange、 zincrby 、zrem
        ZSetOperations zSetOperations = redisTemplate.opsForZSet();

        //1.向有序集合添加一个或多个成员
        zSetOperations.add("zset1","a",10);
        zSetOperations.add("zset1","b",12);
        zSetOperations.add("zset1","c",9);

        //2.通过索引区间返回有序集合中指定区间内的成员
        Set zset1 = zSetOperations.range("zset1", 0, -1);
        System.out.println(zset1);//[c, a, b]

        //3.有序集合中对指定成员的分数加上增量 increment
        zSetOperations.incrementScore("zset1","c",10);

        //4. 移除有序集合中的一个或多个成员
        zSetOperations.remove("zset1","a","b");
```

## 通用操作

```java
        //keys 、exists、 type 、del
        //1.查找所有符合给定模式( pattern)的 key
        Set keys = redisTemplate.keys("*");
        System.out.println(keys);//[set2, zset1, set1]

        //2.检查给定 key 是否存在
        Boolean name = redisTemplate.hasKey("name");
        Boolean set1 = redisTemplate.hasKey("set1");

        //3.返回 key 所储存的值的类型
        for (Object key : keys) {
            DataType type = redisTemplate.type(key);
            System.out.println(type.name());//SET  ZSET  SET
        }

        //4.该命令用于在 key 存在是删除 key
        redisTemplate.delete("mylist");
```
