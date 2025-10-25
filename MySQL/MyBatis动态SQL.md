# &#x20;MyBatis 的 foreach 标签

主要属性说明：

**collection="ids": 指定要遍历的集合参数名称，对应方法中传入的 ids 参数**

**item="id": 定义每次迭代时集合元素的别名，在循环体内通过 #{id} 引用**

**open="(": 循环开始前添加的前缀字符**

**close=")": 循环结束后添加的后缀字符**

**separator=",": 每个元素之间的分隔符**

```sql
<select id="getSetmealIdsByDishIds" resultType="java.lang.Long">
    SELECT setmeal_id FROM sky_take_out.setmeal_dish WHERE dish_id IN
    <foreach collection="ids" item="id" open="(" close=")" separator=",">
        #{id}
    </foreach>
</select>
```

```sql
<insert id="insertBatch">
    INSERT INTO sky_take_out.setmeal_dish (setmeal_id, dish_id, name,
       price, copies) VALUES
    <foreach collection="setmealDishList" item="item" separator=",">
        (#{item.setmealId}, #{item.dishId}, #{item.name},
        #{item.price}, #{item.copies})
    </foreach>
</insert>
```

