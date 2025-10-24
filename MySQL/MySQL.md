# **MySQL**
## 初始化
管理员身份运行cmd
```mysql
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
```mysql
-- 停止mysql服务
net stop mysql
-- 卸载mysql服务
mysqld -remove mysql
```
### 数据库操作
```mysql
-- 查询所有数据库
show databases;
-- 使用数据库
use db_name;
-- 查询当前数据库
select database();
-- 创建数据库
create database [if not exists] db_name [default character utf8mb4];
-- 删除数据库
drop database [if exists] db_name;
```
### SQL功能分类
| 分类 | 说明 |
| :--: | :--: |
| DDL | 用于定义数据库对象(数据库,表,字段) |
| DML | 用于对数据库表中的数据进行增删改 |
| DQL | 用于查询数据库中表的数据 |
| DCL | 用于创建数据库用户,控制数据库的访问权限 |
### 表-DDL
#### 表创建
```mysql
create table table_name(
    字段名 字段类型 [约束] [comment '注释'],
    ...
    字段名 字段类型 [约束] [comment '注释']
) [comment '表注释']
```
#### 约束
```
not null 非空
unique 唯一
primary key 非空且唯一
default 默认值
auto_increment 自动增长
foreign key 外键
```
#### 字段类型  

| 字符串类型 | 描述 | 数值类型 | 描述 | 日期类型 | 描述 |  
| :--: | :--: | :--: | :--: | :--: | :--: |  
| tinyint | 小整数值 | char | 定长字符串 | date | YYYY-MM-DD |
| smallint | 大整数值 | varcahr | 可变长字符串 | time | HH:MM:SS |
| mediumint | 大整数值 | tinyblob | 小于255字符的二进制字符串 | year | YYYY |
| int | 大整数值 | tinytext | 短文本字符串 | datetime | YYYY-MM-DD HH:MM:SS |
| bigint | 极大整数值 | blob | 二进制长字符串 |
| float | 单精度浮点数 | text | 长文本字符串 |
| double | 双精度浮点数 | mediumblob | 中等二进制长字符串 |
| decimal | 小数值(精度更高) | mediumtext | 中等文本字符串 |
| | | longblob | 二进制极长字符串 |
| | | longtext | 极长文本字符串 | 

#### 表的修删查
```mysql
-- 查询所有数据库的表
show tables;
-- 查询表结构
desc table_name;
-- 查询建表语句
show create table table_name;

-- 增加字段
alter table table_name add 字段 类型 [comment '注释'] [约束]
-- 修改字段类型
alter table table_name modify 字段 新类型;
-- 修改字段名和类型
alter table table_name change 旧名 新名 类型 [comment '注释'] [约束];
-- 删除字段
alter table table_name drop column 字段;
-- 修改表名
alter table table_name rename to 新表名;

-- 删除表
drop table [if exists] table_name;
```
### 表-DML
#### 表的增删改
```mysql
-- 指定字段插入
insert into table_name(字段1,字段2) values(值1,值2);
-- 插入所有字段
insert into table_name values(值1,值2,...);
-- 批量插入
insert into table_name(字段1,字段2) values(值1,值2), (值3,值4);
insert into table_name values(值1,值2,值3), (值4,值5,值6);

-- 修改数据
update table_name set 字段1=值1,字段2=值2 [where 条件];

-- 删除数据
delete from table_name [where 条件];
```
### 表-DQL
#### 表的查询语句
- 基本查询
```mysql
-- 查询多个字段
select 字段1,字段2,字段3... from 表名;
-- 查询所有字段
select * from 表名;
-- 为字段起别名
select 字段1 [as 别名1], 字段2 [as 别名2] from 表名;
-- 去重
select distinct 字段1,字段2 from 表名;
```
- 聚合函数  

| 聚合函数 | 功能 |
| :--: | :--: |
| count() | 统计数量 |
| sum() | 求和 |
| avg() | 求平均值 |
| max() | 求最大值 |
| min() | 求最小值 |

```mysql
select 字段列表
from 表名列表
[where 条件列表]
[group by 分组字段]
[having 分组条件]
[order by 排序字段 [asc 升序/desc 降序]]
[limit 索引,数量]
```
> 没有显式指定 GROUP BY 子句时，模式要求 SELECT 列表中的所有非聚合列必须包含在聚合函数中



