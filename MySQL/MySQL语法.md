# MySQL
## 初始化
管理员身份运行cmd
```sql
-- 初始化
mysqld --initialize-insecure
-- 注册mysql服务
mysqld -install
-- 启动mysql服务
net start mysql
-- 修改默认账户密码
mysqladmin -u root password 1234
-- 登录mysql
mysql -uroot -p1234
```
## 卸载MySQL
```sql
-- 停止mysql服务
net stop mysql
-- 卸载mysql服务
mysqld -remove mysql
```
## MySQL 用户管理
### 查看用户
```sql
# 查看所有用户
SELECT * FROM mysql.user;
# 查看当前用户
SELECT USER();
```
### 创建用户
```sql
-- 创建用户
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
```
- username：用户名。
- host：指定用户可以从哪些主机连接。例如，**localhost 仅允许本地连接，% 允许从任何主机连接**。
- password：用户的密码。
### 权限管理
#### 授予权限
```sql
GRANT privileges ON database_name.* TO 'username'@'host';
```
- privileges：所需的权限，如 ALL PRIVILEGES、SELECT、INSERT、UPDATE、DELETE 等。
- database_name.*：表示对某个数据库或表授予权限。database_name.* 表示对整个数据库的所有表授予权限，database_name.table_name 表示对指定的表授予权限。
- TO 'username'@'host'：指定授予权限的用户和主机。
#### 刷新权限
```sql
FLUSH PRIVILEGES;
```
#### 查看权限
```sql
SHOW GRANTS FOR 'username'@'host';
```
#### 撤销权限
```sql
REVOKE privileges ON database_name.* FROM 'username'@'host';
```
- privileges：撤销的权限。
####  删除用户
```sql
DROP USER 'username'@'host';
```
### 修改用户密码
```sql
ALTER USER 'username'@'host' IDENTIFIED BY 'new_password';
```
### 修改用户主机
先删除用户，再重新创建一个新的用户，指定新的主机。
## MySQL 管理
### 查看数据库
```sql
-- 查询所有数据库
SHOW DATABASES;
-- 显示当前数据库
SELECT DATABASE();
```
### 使用数据库
```sql
-- 使用数据库
USE db_name;
```
- db_name：数据库名称。
### 显示指定数据库的所有表
```sql
-- 查询当前数据库的所有表
SHOW TABLES;
-- 查询指定数据库的所有表
SHOW TABLES FROM db_name;
```
### 查看建表语句
```sql
-- 查询指定数据库的表的建表语句
SHOW CREATE TABLE table_name;
```
### 显示表结构
```sql
-- 查询指定数据库的表的结构
SHOW COLUMNS FROM table_name;
-- 或者
DESC table_name;
```
### 显示数据表的详细索引信息
```sql
-- 查询指定数据库的表的详细索引信息
SHOW INDEX FROM table_name FROM db_name;

SHOW INDEX FROM db_name.table_name;

SHOW INDEX FROM table_name;
```
## MySQL 数据库管理
### 创建数据库
```sql
CREATE DATABASE [IF NOT EXISTS] database_name
  [CHARACTER SET charset_name]
  [COLLATE collation_name];
```
- IF NOT EXISTS：如果数据库已经存在，则不创建。
- CHARACTER SET charset_name：指定数据库的字符集。
- COLLATE collation_name：指定数据库的排序规则。
### 删除数据库
```sql
DROP DATABASE [IF EXISTS] database_name;
```
### 使用数据库
```sql
-- 使用数据库
USE db_name;
```
## 字段类型  

| 数值类型 | 描述 | 字符串类型 | 描述 | 日期类型 | 描述 |  
| :--: | :--: | :--: | :--: | :--: | :--: |  
| tinyint | 小整数值 | char | 定长字符串 | date | YYYY-MM-DD |
| smallint | 大整数值 | varchar | 可变长字符串 | time | HH:MM:SS |
| mediumint | 大整数值 | tinyblob | 小于255字符的二进制字符串 | year | YYYY |
| int | 大整数值 | tinytext | 短文本字符串 | datetime | YYYY-MM-DD HH:MM:SS |
| bigint | 极大整数值 | blob | 二进制长字符串 | timestamp | 时间戳 |
| float | 单精度浮点数 | text | 长文本字符串 | | |
| double | 双精度浮点数 | mediumblob | 中等二进制长字符串 | | |
| decimal | 小数值(精度更高) | mediumtext | 中等文本字符串 | | |
| | | longblob | 二进制极长字符串 |
| | | longtext | 极长文本字符串 | 
## 约束
```
not null 非空
unique 唯一
primary key 非空且唯一
default 默认值
auto_increment 自动增长
foreign key 外键
```
## SQL语句分类
| 分类 | 说明 |
| :--: | :--: |
| DDL | 用于定义数据库对象(数据库,表,字段) |
| DML | 用于对数据库表中的数据进行增删改 |
| DQL | 用于查询数据库中表的数据 |
| DCL | 用于创建数据库用户,控制数据库的访问权限 |
## 数据表
### 创建表
```sql
CREATE TABLE [IF NOT EXISTS] table_name (
    字段名 字段类型 [约束] [COMMENT '注释'],
    ...,
    [PRIMARY KEY (字段名)],
    [FOREIGN KEY (字段名) REFERENCES 关联表(关联字段)]
) [COMMENT '表注释'] 
[ENGINE=engine_name] 
[CHARACTER SET charset_name] 
[COLLATE collation_name];
```
- IF NOT EXISTS：如果表已经存在，则不创建。
- COMMENT '注释'：为表添加注释。
- PRIMARY KEY (字段名)：指定主键字段。
- FOREIGN KEY (字段名) REFERENCES 关联表(关联字段)：指定外键字段和关联的表和字段。
- ENGINE=engine_name：指定表的存储引擎，如 InnoDB、MyISAM 等。
- CHARACTER SET charset_name：指定表的字符集。
- COLLATE collation_name：指定表的排序规则。
### MySQL UNION 操作符
- UNION 语句：用于将不同表中相同列中查询的数据展示出来；（不包括重复数据）
- UNION ALL 语句：用于将不同表中相同列中查询的数据展示出来；（包括重复数据）
```sql
SELECT 列名称 FROM 表名称 UNION SELECT 列名称 FROM 表名称 ORDER BY 列名称;
SELECT 列名称 FROM 表名称 UNION ALL SELECT 列名称 FROM 表名称 ORDER BY 列名称；
```
### 删除表
```sql
DROP TABLE [IF EXISTS] table_name;
```
### 表中插入数据
```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```
### 表中查询数据
```sql
SELECT [DISTINCT] column1 [AS alias1], column2 [AS alias2], ...
FROM table_name [AS table_alias]
[WHERE condition]
  [AND | OR additional_condition]
  [LIKE 'pattern']
  [REGEXP | RLIKE 'pattern']
  [[NOT] IN (value1, value2, ...)]
  [[NOT] BETWEEN value1 AND value2]
  [[NOT] IS NULL]
[GROUP BY column_name1, column_name2, ... [HAVING condition]]
[ORDER BY column_name1 [ASC | DESC], column_name2 [ASC | DESC], ...]
[LIMIT [offset,] number];
```

- **DISTINCT**：去除结果集中的重复记录。
- **AS alias**：为列或表指定别名，简化查询并自定义结果集列名。
- **WHERE condition**：指定过滤条件，只返回符合条件的行。
  - **AND | OR | NOT**：逻辑运算符，用于组合多个条件。
  - **LIKE pattern**：模糊查询，`%`表示任意个字符，`_`表示单个字符。
  - **REGEXP | RLIKE**：正则表达式匹配，两者功能相同。
  - **[NOT] IN**：检查值是否在指定列表中。
  - **[NOT] BETWEEN**：检查值是否在指定范围内。
  - **[NOT] IS NULL**：检查值是否为NULL。
- **GROUP BY**：按指定列对结果集分组，常用于聚合函数。
- **HAVING**：分组后的数据过滤，与WHERE不同，它作用于分组结果。
- **ORDER BY**：指定结果集排序，可多列排序，默认升序(ASC)。
- **LIMIT**：限制返回行数，`LIMIT offset, number`表示从offset位置开始返回number行。
> 在MySQL的ONLY_FULL_GROUP_BY模式下，查询必须遵循以下规则：
> - 当使用GROUP BY子句时：SELECT列表中的所有非聚合列必须包含在GROUP BY子句中
> - 当没有GROUP BY子句但使用了聚合函数时：SELECT列表中不能有非聚合列，所有列都必须使用聚合函数处理
#### 聚合函数
常用聚合函数与GROUP BY配合使用：
- **COUNT(column)**：统计非NULL值的数量
- **SUM(column)**：计算数值列的总和
- **AVG(column)**：计算数值列的平均值
- **MAX(column)**：找出列中的最大值
- **MIN(column)**：找出列中的最小值
#### 正则表达式匹配的字符类  
> .：匹配任意单个字符。  
> ^：匹配字符串的开始。  
> $：匹配字符串的结束。  
> *：匹配零个或多个前面的元素。  
> +：匹配一个或多个前面的元素。  
> ?：匹配零个或一个前面的元素。  
> [abc]：匹配字符集中的任意一个字符。  
> [^abc]：匹配除了字符集中的任意一个字符以外的字符。  
> [a-z]：匹配范围内的任意一个小写字母。  
> [0-9]：匹配一个数字字符。  
> \w：匹配一个字母数字字符（包括下划线）。  
> \s：匹配一个空白字符。
### 修改表中数据
```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```
### 删除表中的数据
```sql
DELETE FROM table_name
WHERE condition;
```
## MySQL 连接
### INNER JOIN（内连接,或等值连接）：获取两个表中字段匹配关系的记录。
![img](https://img2024.cnblogs.com/blog/3471133/202511/3471133-20251123231309111-1688115681.png)
```sql
SELECT column1, column2, ...
FROM table1 INNER JOIN table2 
ON table1.column_name = table2.column_name;
```
### LEFT JOIN（左连接）：获取左表所有记录，即使右表没有对应匹配的记录。
![img](https://img2024.cnblogs.com/blog/3471133/202511/3471133-20251123231345213-918900682.png)
```sql
SELECT column1, column2, ...
FROM table1 LEFT JOIN table2 
ON table1.column_name = table2.column_name;
```
### RIGHT JOIN（右连接）： 与 LEFT JOIN 相反，用于获取右表所有记录，即使左表没有对应匹配的记录。
![img](https://img2024.cnblogs.com/blog/3471133/202511/3471133-20251123231405714-2078248523.png)
```sql
SELECT column1, column2, ...
FROM table1 RIGHT JOIN table2 
ON table1.column_name = table2.column_name;
```
## MySQL ALTER语句
> MySQL 的 ALTER 命令用于修改数据库、表和索引等对象的结构。

### 修改表名
```sql
-- 修改表名
ALTER TABLE table_name
RENAME TO new_table_name;
```

### 增加列
```sql
-- 增加单列
ALTER TABLE table_name
ADD COLUMN column_name data_type [约束条件];

-- 增加多列（用逗号分隔）
ALTER TABLE table_name
ADD COLUMN column1 data_type [约束],
ADD COLUMN column2 data_type [约束];
```

### 修改列定义
```sql
-- 修改列数据类型和约束
ALTER TABLE table_name
MODIFY COLUMN column_name new_data_type [新约束];
```

### 修改列名及数据类型
```sql
-- 同时修改列名和数据类型
ALTER TABLE table_name
CHANGE COLUMN old_column_name new_column_name new_data_type [约束];
```

### 删除列
```sql
-- 删除单列
ALTER TABLE table_name
DROP COLUMN column_name;

-- 删除多列（用逗号分隔）
ALTER TABLE table_name
DROP COLUMN column1,
DROP COLUMN column2;
```

### 增加删除主键 PRIMARY KEY 约束
```sql
-- 增加单列主键
ALTER TABLE table_name
ADD PRIMARY KEY (column_name);

-- 增加复合主键
ALTER TABLE table_name
ADD PRIMARY KEY (column1, column2);

-- 删除主键
ALTER TABLE table_name
DROP PRIMARY KEY;
```
### 添加外键 FOREIGN KEY 约束
```sql
-- 基本语法
ALTER TABLE child_table
ADD CONSTRAINT fk_name 
FOREIGN KEY (child_column) 
REFERENCES parent_table (parent_column);

-- 带级联操作的语法
ALTER TABLE child_table
ADD CONSTRAINT fk_name 
FOREIGN KEY (child_column) 
REFERENCES parent_table (parent_column)
[ON DELETE {NO ACTION | CASCADE | SET NULL | RESTRICT}]
[ON UPDATE {NO ACTION | CASCADE | SET NULL | RESTRICT}];
```

**说明**：
- `ADD CONSTRAINT fk_name`：添加名为fk_name的外键约束，约束名建议采用语义化命名（如fk_child_parent_column）
- `FOREIGN KEY (child_column)`：指定子表中作为外键的列名（可指定多个列作为复合外键）
- `REFERENCES parent_table (parent_column)`：指定外键引用的父表及其列名，父表被引用列必须具有唯一性约束（主键或唯一索引）

**级联操作选项**：
- `ON DELETE CASCADE`：当父表记录被删除时，自动删除子表中相关记录
- `ON DELETE SET NULL`：当父表记录被删除时，将子表中对应外键列值设为NULL（需确保外键列允许NULL值）
- `ON DELETE RESTRICT/NO ACTION`：不允许删除父表中已被子表引用的记录
- `ON UPDATE CASCADE`：当父表主键值更新时，自动更新子表中的外键值
- `ON UPDATE SET NULL`：当父表主键值更新时，将子表中对应外键列值设为NULL

**注意事项**：
1. 创建外键时，子表和父表的存储引擎必须相同且支持事务（如InnoDB）
2. 外键列和引用列的数据类型必须完全匹配（包括长度和符号属性）
3. 外键约束会影响写入性能，不建议在高并发写入场景大量使用
4. 可通过`SHOW CREATE TABLE child_table;`查看外键约束详情
### 删除外键约束
```sql
ALTER TABLE child_table DROP FOREIGN KEY fk_name;
```
### 修改表的存储引擎
```sql
-- 修改表的存储引擎
ALTER TABLE table_name
ENGINE = engine_name;  -- 例如 InnoDB, MyISAM 等
```
## MySQL 事务
> 事务是必须满足4个条件（ACID）：  
> **原子性（Atomicity，或称不可分割性）、一致性（Consistency）、隔离性（Isolation，又称独立性）、持久性（Durability）。**
- **原子性**：一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。

- **一致性**：在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。

- **隔离性**：数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括**读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）**。

- **持久性**：事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。
### 事务操作
#### 开始事务
```sql
BEGIN; -- 或者使用 START TRANSACTION;
```
#### 提交事务
```sql
COMMIT;
```
#### 回滚事务
```sql
ROLLBACK;
```
#### 事务中设置保存点
```sql
SAVEPOINT savepoint_name;
```
#### 回滚到设置的保存点
```sql
ROLLBACK TO SAVEPOINT savepoint_name;
```
#### 删除事务中设置的保存点
```sql
RELEASE SAVEPOINT savepoint_name;
```
> 事务提交(COMMIT)或完全回滚(ROLLBACK)时，所有保存点会自动释放
### 事务隔离级别
![img](https://img2024.cnblogs.com/blog/3471133/202511/3471133-20251125115004693-1431931328.png)
- **READ-UNCOMMITTED（读未提交）**：最低级别，允许读未提交数据，存在脏读、不可重复读、幻读问题。
- **READ-COMMITTED（读已提交）**：仅允许读已提交数据，可避免脏读，但仍有不可重复读、幻读问题。
- **REPEATABLE-READ（可重复读）**：保证同一事务内多次读取相同数据得到一致结果，防止脏读和不可重复读，但理论上存在幻读可能。
- **SERIALIZABLE（串行化）**：最高级别，事务顺序执行，避免所有并发问题，但性能较低。
> 注意： 在 MySQL 的 InnoDB 引擎中，通过 Next-Key Locking 技术，在可重复读（Repeatable Read） 隔离级别下就已经可以避免绝大部分的幻读现象。这是 MySQL 对标准隔离级别的增强，也是其默认使用该级别的重要原因。
### 并发事务并发的三个问题
#### **脏读 (Dirty Read)**
一个事务读到了另一个未提交事务修改的数据。如果另一个事务中途回滚，那么第一个事务读到的数据就是“脏”的、无效的。
#### **不可重复读 (Non-Repeatable Read)**
一个事务内，两次读取同一个数据项，得到了不同的结果。重点在于另一个已提交事务对数据进行了修改（UPDATE）。
#### **幻读 (Phantom Read)**
一个事务内，两次执行同一个查询，返回的结果集行数不同。重点在于另一个已提交事务对数据进行了增删（INSERT/DELETE），像产生了幻觉一样。  
> 不可重复读 vs 幻读：    
> - 不可重复读针对的是某一行数据的值被修改（UPDATE）。  
> - 幻读针对的是结果集的行数发生变化（INSERT/DELETE）。  
## MySQL 索引
> 索引是数据库中提高查询效率的关键，索引的创建和删除需要消耗一定的时间，所以索引的创建和删除应该在业务量小的时候进行。
### 索引类型
| 索引类型 | 定义 | 核心特性 |
|---------|------|--------|
| 主键索引（PRIMARY KEY） | 用于唯一标识表中每条记录的特殊索引 | 1. 唯一且非空（自动NOT NULL + UNIQUE）；<br>2. 默认创建聚簇索引；<br>3. 一张表只能有一个主键 |
| 唯一索引（UNIQUE） | 确保索引列值唯一性的索引 | 1. 索引列值唯一；<br>2. 允许NULL值（多个NULL不冲突）；<br>3. 一张表可创建多个唯一索引 |
| 普通索引（INDEX） | 最基础的索引类型，无唯一性限制 | 1. 允许重复值和NULL值；<br>2. 一张表可创建多个普通索引；<br>3. 非聚簇索引 |
| 全文索引（FULLTEXT） | 专用于长文本字段关键词搜索的索引 | 1. 仅支持CHAR、VARCHAR、TEXT类型；<br>2. 支持自然语言和布尔搜索；<br>3. 忽略停用词 |
| 组合索引（Composite Index） | 基于多个字段联合创建的索引 | 1. 遵循"最左前缀原则"；<br>2. 字段顺序影响查询效率；<br>3. 可包含唯一约束 |

## MySQL 临时表
## MySQL 复制表
## MySQL 元数据