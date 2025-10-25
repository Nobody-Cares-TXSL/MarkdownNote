# 字符串操作

```sql
SET key value ：设置指定key的值
GET key： 获取指定key的值
SETEX key seconds value ：设置指定key的值，并将 key 的过期时间设为 seconds 秒
SETNX key value ：只有在 key 不存在时设置 key 的值
```

# 哈希操作

```sql
HSET key field value ：将哈希表 key 中的字段 field 的值设为 value
HGET key field ： 获取存储在哈希表中指定字段的值
HDEL key field ： 删除存储在哈希表中的指定字段
HKEYS key ： 获取哈希表中所有字段
HVALS key ： 获取哈希表中所有值
```

# 列表操作

```sql
LPUSH key value1 [value2] ： 将一个或多个值插入到列表头部
LRANGE key start stop ： 获取列表指定范围内的元素
RPOP key ： 移除并获取列表最后一个元素，并且这个命令会返回这个值
LLEN key ： 获取列表长度
BRPOP key1 [key2 ] timeout ： 
移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超 时或发现可弹出元素为止
```

# 集合操作

```sql
SADD key member1 [member2] ： 向集合添加一个或多个成员
SMEMBERS key ： 返回集合中的所有成员
SCARD key ： 获取集合的成员数
SINTER key1 [key2] ： 返回给定所有集合的交集
SUNION key1 [key2] ： 返回所有给定集合的并集
SREM key member1 [member2] ： 移除集合中一个或多个成员
```

# 有序集合操作

```sql
ZADD key score1 member1 [score2 member2] ： 向有序集合添加一个或多个成员
ZRANGE key start stop [WITHSCORES] ： 通过索引区间返回有序集合中指定区间内的成员
WITHSCORES：加上之后再返回这个成员的时候，也会把这个成员的分数给我们返回。
ZINCRBY key increment member ： 有序集合中对指定成员的分数加上增量 increment
改变这个集合中元素的分数的，元素的分数可以修改
ZREM key member [member …] ： 移除有序集合中的一个或多个成员
```

# 通用命令

```sql
KEYS pattern ： 查找所有符合给定模式( pattern)的 key
EXISTS key ： 检查给定 key 是否存在
TYPE key ：返回 key 所储存的值的类型
DEL key ： 该命令用于在 key 存在是删除 key
```
