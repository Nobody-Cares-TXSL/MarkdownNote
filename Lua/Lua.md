# Lua
## 注释
- 单行注释
```lua
-- 这是一个单行注释
```
- 多行注释
```lua
--[[
这是一个多行注释
]]
```
> 多行注释以 `--[[` 开始，并以 `]]` 结束  
## 数据类型
> Lua 是动态类型语言，变量不要类型定义,只需要为变量赋值。  

| 数据类型 | 描述 |
| :--: | :--: |
| nil | 无效值，相当于false |
| boolean | 布尔值：false和true |
| number | 双精度浮点数 |
| string | 字符串（单引号或双引号） |
| function | C或Lua编写的函数 |
| userdata | C数据结构 |
| thread | 独立执行线路，用于协同程序 |
| table | 关联数组，索引可以是数字或字符串 |
### nil
- nil表示无效值，相当于false
### boolean
- 布尔值：false和true
>  false和nil都为false，其他值均为true, 0为true
### number
- 双精度浮点数
>  Lua 不支持整数类型，所有的数字都是浮点数。
### string
- 字符串（单引号或双引号）
- 字符串可以使用 .. 运算符连接
- 可以使用 # 运算符获取字符串的长度
```lua
str = "Hello, World!"
print(#str) -- 13
```
- 也可以用`[[]]`表示多行字符串
```lua
str = [[
这是一个多行字符串
]]
```
> 对一个数字字符串上进行算术操作时，Lua 会尝试将这个数字字符串转成一个数字, 如果转换失败，则报错
### table (表)
#### 表的域键和值
```lua
-- 键可以是数字、字符串，甚至其他表
myTable = {
    [1] = "数字键",        -- 数字键
    ["name"] = "字符串键",  -- 字符串键
    [true] = "布尔键",     -- 布尔键
    [{a=1}] = "表键"       -- 表作为键
}

-- 值也可以是任意类型
myTable.value = 42           -- 数字值
myTable.text = "hello"       -- 字符串值
myTable.flag = true          -- 布尔值
myTable.list = {1, 2, 3}     -- 表值
myTable.func = function()    -- 函数值
    print("这是一个函数")
end
```
> - 不指定键的值会被自动分配数字键（从1开始）
> - 将表元素的值设为`nil`即可删除该元素
> - 索引可以是数字或字符串
> - 可以使用`table.key`或`table["key"]`方式访问字符串键
> - 可以使用`table[index]`方式访问数字键, `index`从1开始
> - table 不会固定长度大小, 可以添加任意数量的元素
#### 访问域的方式
```lua
person = {
    name = "张三",
    age = 25,
    [1] = "第一个元素"
}

-- 方式1：使用点号访问字符串键
print(person.name)    -- 输出: 张三

-- 方式2：使用方括号访问任意类型的键
print(person["age"])  -- 输出: 25
print(person[1])      -- 输出: 第一个元素

-- 方式3：使用变量作为键
key = "name"
print(person[key])    -- 输出: 张三
```
### function (函数)
- 通过`function`关键字来定义函数
- 函数可以存在变量里
- function 可以以匿名函数（anonymous function）的方式通过参数传递
```lua
function testFun(tab, fun)
    for k, v in pairs(tab) do
        print(fun(k, v));
    end
end

tab = { key1 = "val1", key2 = "val2" };
-- 变量存储匿名函数
fun = function(key, val) --匿名函数
    return key .. "=" .. val;
end 
testFun(tab, fun);
```
### thread (线程)
> 线程是一种独立的执行线路，用于协同程序，在 Lua 中可以通过 coroutine 库来创建和操作线程。

### userdata (自定义类型)
> 用于表示一种由应用程序或 C/C++ 语言库所创建的类型，可以将任意 C/C++ 的任意数据类型的数据（通常是 struct 和 指针）存储到 Lua 变量中调用。
## Lua 变量
### 变量类型
- 全局变量
> Lua 中的变量全是全局变量，哪怕是语句块或是函数里
- 局部变量
> 用 local 显式声明的变量为局部变量
- 表中的域
```lua
a = 5               -- 全局变量
local b = 5         -- 局部变量

function joke()
    c = 5           -- 全局变量
    local d = 6     -- 局部变量
end

joke()
print(c,d)          --> 5 nil

do 
    -- 创建了一个新的局部变量a，它遮蔽了外部的全局变量a
    local a = 6     -- 局部变量
    b = 6           -- 对局部变量重新赋值
    print(a,b);     --> 6 6
end
-- 退出do...end块后，内部的局部变量a被销毁
-- 外部的全局变量a恢复可见，值仍为5
print(a,b)      --> 5 6
```
### 赋值语句
- 多变量同时赋值
```lua
a, b, c = 1, 2, 3 -- 1 2 3
```
> 变量列表和值列表的各个元素用逗号分开，赋值语句右边的值会依次赋给左边的变量  
> 赋值语句Lua会**先计算右边所有的值然后再执行赋值操作**  
- 变量个数和值的个数不一致时
```lua
a. 变量个数 > 值的个数             按变量个数补足nil
b. 变量个数 < 值的个数             多余的值会被忽略
```
## 循环
### while 循环
```lua
while(condition)
do
   statements
end
```
> - statements(循环体语句) 可以是一条或多条语句
> - condition(条件) 可以是任意表达式
> - 在 condition(条件) 为 true 时执行循环体语句。
### for 循环
#### 数值for循环
```lua
for var=exp1,exp2,exp3 do  
    <执行体>  
end  
```
> - var 从 exp1 变化到 exp2
> - 每次变化以 exp3 为步长递增 var，并执行一次 "执行体"
> - exp3 是可选的，如果不指定，默认为1
```lua
function f(x)
    return x*2
end
for i=1,f(2) do
    -- io.write() 不会添加换行符
    io.write(i .. " ")
end
-- 1 2 3 4 
```
> **for循环表达式求值机制**：for循环的三个表达式（初始值、结束值、步长）在循环开始前只计算一次，即使循环过程中这些值发生变化也不会影响循环次数
#### 泛型for循环
> 泛型 for 循环通过一个迭代器函数来遍历所有值
```lua
--打印数组a的所有值  
a = {"one", "two", "three"}
for i, v in ipairs(a) do
    print(i, v)
end
-- 1 one
-- 2 two
-- 3 three
```
### repeat...until 循环
```lua
repeat
   statements
until( condition )
```
> condition 条件为 true 时结束循环体语句。  
> **反人类设计**
### break
- break 跳出当前循环
### goto
- goto 控制流程无条件地转到被标记的语句处
> goto语句为 **Lua 5.2+** 的新特性，在Lua 5.1及更早版本中不支持
```lua
local a = 1
::label::
print("--- goto label ---")
a = a + 1
if a < 3 then
    goto label   -- a 小于 3 的时候跳转到标签 label
end
-- 输出为:
--- goto label ---
--- goto label ---
```

#### 在goto标签中设置多个语句
```lua
i = 0
::s1::
do
    print(i)
    i = i + 1
end
if i > 3 then
    os.exit()   -- i 大于 3 时退出
end
goto s1
```

#### goto语句的限制
1. 不能跳入一个函数块内部
2. 不能跳出函数块
3. 不能跳过局部变量的声明
## 条件判断
### if 语句
```lua
if( condition ) then
   statements
end
```
### else 语句
```lua
if( condition ) then
   statements
else
   statements
end
```
### elseif 语句
> elseif 语句和 else 语句类似，但是 elseif 语句可以有多个
```lua
if( condition ) then
   statements
elseif( condition ) then
   statements
else
   statements
end
```
## 函数
### 函数定义
```lua
local function 函数名(参数1, 参数2, ...)
    -- 函数体
    return 返回值1, 返回值2, ...
end
```
> - 默认为全局函数，使用`local`关键字创建局部函数
> - 函数可以有多个返回值
> - 函数可以作为参数传递给函数
```lua
myprint = function(param)
   print(param)
end

function add(num1,num2,functionPrint)
   result = num1 + num2
   -- 调用传递的函数参数
   functionPrint(result)
end
-- myprint 函数作为参数传递
add(2,5,myprint) -- 7
```
### 可变参数
- 函数定义时使用`...`表示可变参数
- `select("#", ...)`获取可变参数的数量
- `select(index, ...)`获取第n个可变参数的值
> 当`select`函数的返回值赋给变量时，返回从指定索引开始的单个参数  
> 当`select`函数直接用于表达式或打印时，返回从指定索引到末尾的所有参数序列
```lua
function average(x, y, ...)
   result = 0
   local arg={x,y,...}
   for i,v in ipairs(arg) do
      print(i,v)
      result = result + v
   end
   print("总共传入参数 " .. #arg .. " 个数") -- 总共传入参数 6 个数
   print("总共传入可变参数 " .. select("#",...) .. " 个数") -- 总共传入可变参数 4 个数
   return result/select("#",...)
end
print("平均值为",average(10,5,3,4,5,6)) -- 平均值为 8.25
```
## 运算符
### 算术运算符
| 运算符 | 名称 | 说明 |
|--------|------|------|
| + | 加法 | 两数相加 |
| - | 减法 | 两数相减 |
| * | 乘法 | 两数相乘 |
| / | 除法 | 两数相除（浮点数除法） |
| % | 取余 | 获取除法余数 |
| ^ | 乘幂 | 计算幂次方 |
| - | 负号 | 取负值 |
| // | 整除运算符 | 整数除法（Lua 5.3及以上版本支持） |
### 关系运算符
| 运算符 | 名称 | 说明 |
|--------|------|------|
| == | 等于 | 比较两个值是否相等 |
| ~= | 不等于 | 比较两个值是否不相等 |
| > | 大于 | 比较左侧值是否大于右侧值 |
| < | 小于 | 比较左侧值是否小于右侧值 |
| >= | 大于等于 | 比较左侧值是否大于或等于右侧值 |
| <= | 小于等于 | 比较左侧值是否小于或等于右侧值 |
### 逻辑运算符
| 运算符 | 名称 | 说明 |
|--------|------|------|
| and | 逻辑与 | 两边都为真时结果为真，否则为假 |
| or | 逻辑或 | 两边有一个为真时结果为真，否则为假 |
| not | 逻辑非 | 取反操作，真变假，假变真 |
### 其他运算符
| 运算符 | 名称 | 说明 |
|--------|------|------|
| .. | 连接运算符 | 连接两个字符串 |
| # | 长度运算符 | 获取字符串或表的长度 |
| . | 点运算符 | 访问表的字段 |
| : | 冒号运算符 | 调用对象的方法（隐式传递self） |
| [ ] | 索引运算符 | 访问表的元素 |
| = | 赋值运算符 | 给变量赋值 |
## 数组
### 多维数组
- 多维数组使用方括号表示，如`a[i][j]`
- 多维数组的索引从1开始
- 多维数组的索引可以是任意值，如`a[1]["key"]`
```lua
a = {
    [1] = {1,2,3},
    [2] = {4,5,6}
}
print(a[1][2])
```
## 迭代器
> 迭代器是一种特殊的函数，用于遍历集合中的元素  
> Lua 中的迭代器分为无状态迭代器和有状态迭代器两种类型  
> 当迭代器中的状态参数为nil时，循环结束
### 无状态迭代器
- 无状态迭代器不保留任何状态，每次调用时根据传入的参数计算下一个值
- for循环语法：`for 变量们 in 工厂函数, 固定参数, 初始状态 do`
- 工厂函数接收两个参数：固定参数和当前状态
- 返回新的状态和对应的值，当返回nil时循环结束
```lua
function square(iteratorMaxCount, currentNumber)
   if currentNumber < iteratorMaxCount then
      currentNumber = currentNumber + 1
      return currentNumber, currentNumber * currentNumber
   end
end

for i, n in square, 3, 0 do
   print(i, n)
end
-- 输出:
-- 1	1
-- 2	4
-- 3	9
```
> 无状态迭代器的优点是高效，因为不需要创建闭包来保存状态

### 有状态迭代器
- 有状态迭代器使用闭包来保存状态
- 工厂函数返回一个内部函数，该函数保留了外部变量（状态）
- 每次调用内部函数时，修改并返回新的状态
```lua
function squareIterator(max)
   local current = 0
   return function()
      current = current + 1
      if current <= max then
         return current, current * current
      end
   end
end

for i, n in squareIterator(3) do
   print(i, n)
end
-- 输出:
-- 1	1
-- 2	4
-- 3	9
```
> 有状态迭代器的优点是使用更简单，不需要传递状态参数  
## Table
[基本操作点击跳转](#table-表)  
### table 操作
#### table.concat(table, [sep, [start, [end]]])
- table：要连接的表
- sep：可选，连接符，默认值为空字符串
- start：可选，开始连接的索引，默认值为1
- end：可选，结束连接的索引，默认值为表的长度
```lua
fruits = {"apple", "banana", "orange"}

-- 基本用法
print(table.concat(fruits))           -- applebananaorange

-- 使用连接符
print(table.concat(fruits, ", "))     -- apple, banana, orange

-- 指定范围
print(table.concat(fruits, ", ", 2, 3))  -- banana, orange
```
#### table.insert (table, [pos,] value)
- table：要操作的表
- pos：可选，插入位置的索引，默认值为表的末尾
- value：要插入的值
```lua
fruits = {"apple", "orange"}

-- 在末尾插入
table.insert(fruits, "banana")
print(table.concat(fruits, ", "))  -- apple, orange, banana

-- 在指定位置插入
table.insert(fruits, 2, "grape")
print(table.concat(fruits, ", "))  -- apple, grape, orange, banana
```
#### table.remove (table, [pos])
- table：要操作的表
- pos：可选，要删除的元素的索引，默认值为表的末尾
```lua
fruits = {"apple", "banana", "orange", "grape"}

-- 删除末尾元素
local removed = table.remove(fruits)
print(removed)                         -- grape
print(table.concat(fruits, ", "))      -- apple, banana, orange

-- 删除指定位置元素
removed = table.remove(fruits, 2)
print(removed)                         -- banana
print(table.concat(fruits, ", "))      -- apple, orange
```
#### table.sort (table [, comp])
- table：要排序的表
- comp：可选，排序函数，默认值为升序排序
```lua
-- 升序排序（默认）
nums = {3, 1, 4, 1, 5, 9}
table.sort(nums)
print(table.concat(nums, ", "))  -- 1, 1, 3, 4, 5, 9

-- 降序排序
table.sort(nums, function(a, b) return a > b end)
print(table.concat(nums, ", "))  -- 9, 5, 4, 3, 1, 1

-- 按字符串长度排序
words = {"apple", "pie", "banana"}
table.sort(words, function(a, b) return #a < #b end)
print(table.concat(words, ", "))  -- pie, apple, banana
```
#### table.getn(table)
- table：要获取长度的表
> **已废弃**：该函数在 Lua 5.1 之后被废弃，推荐使用 `#` 运算符代替

```lua
fruits = {"apple", "banana", "orange"}

-- 不推荐：table.getn (Lua 5.0 及更早版本)
-- print(table.getn(fruits))  -- 3

-- 推荐：使用 # 运算符
print(#fruits)  -- 3
```
> - `#` 运算符返回表的长度（从索引1开始的连续整数索引的个数）
> - 如果表中有 "空洞"（中间存在 nil），长度可能不确定
```lua
-- 有空洞的表
t = {1, 2, nil, 4}
print(#t)  --结果不确定
```
## 模块与包
## 元表(Metatable)
## 协同程序(Coroutine)
## 文件I/O
## 错误处理


## 附录
### 常用函数
#### type(value)
- 返回值的类型
```lua
x = nil
print(type(x) == nil) -- false
print(type(x) == "nil") -- true
```
> type(X) 实质是返回的 "nil" 字符串，是一个 string 类型
#### string.find()
> 返回匹配串"开始和结束的下标"（如果不存在匹配串返回nil）
```lua
s, e = string.find("hello world", "world")
print(s, e)
-- 7	11
```
#### pairs() 函数
- pairs()函数会遍历表中的所有键值对
> pairs()函数会忽略nil值
```lua
tab1 = { key1 = "val1", key2 = "val2", "val3" }
for k, v in pairs(tab1) do
    print(k .. " - " .. v)
end
 
tab1.key1 = nil
for k, v in pairs(tab1) do
    print(k .. " - " .. v)
end
```

