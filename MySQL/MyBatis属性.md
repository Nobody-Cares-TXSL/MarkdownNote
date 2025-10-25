**useGeneratedKeys="true"**

* 作用：告诉MyBatis使用JDBC的`getGeneratedKeys`方法来获取数据库自动生成的主键值

**keyProperty="id"**

* 作用：指定将数据库生成的主键值赋给Java对象中的哪个属性

* 命名规则：这里的`id`必须与Java实体类中的属性名完全对应（区分大小写）

