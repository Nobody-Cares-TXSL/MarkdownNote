# 左连接

核心特性

**保留左表全部数据：无论右表是否有匹配记录，左表的所有行都会被包含在结果中。**

**右表仅返回匹配数据：右表只返回与左表满足连接条件的行，不匹配的行以 NULL 填充。**

```sql
select s.*, c.name as categoryName from setmeal s
left join category c on s.category_id = c.id
```
