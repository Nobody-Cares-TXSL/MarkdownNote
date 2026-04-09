# Shell 数组

Bash 只支持**一维数组**，无需预定义大小，下标从 0 开始。

## 定义数组

```bash
# 方式一：括号定义，空格分隔
arr=(A B "C" D)

# 方式二：逐个赋值
arr[0]=A
arr[1]=B
arr[20]=X   # 可以跳着赋值，长度仍为 3（非 21）

# 方式三：Glob 模式批量赋值
files=("$DIR"/*/*.gif)    # 匹配 $DIR 下两层子目录中的 .gif 文件
# () — 数组语法
# */*.gif — 通配符展开，每个匹配项成为数组元素
# "$DIR" — 双引号保护路径中的空格
```

## 读取元素

```bash
echo ${arr[0]}       # 第一个元素
echo ${arr[@]}       # 所有元素
echo ${arr[*]}       # 所有元素（作为单个字符串）
echo ${!arr[@]}      # 所有键（下标）
echo ${#arr[@]}      # 数组长度
```

## 关联数组（类似 Map）
> 关联数组的键必须唯一，必须用 `declare -A` 声明。

```bash
declare -A site
site["google"]="www.google.com"

echo ${site["google"]}     # www.google.com
echo ${!site[@]}           # google
```


## 遍历数组

```bash
arr=(a b c d)

# for 循环（推荐）
for i in "${arr[@]}"; do
    echo $i
done

# 带下标遍历
for i in "${!arr[@]}"; do
    echo "$i -> ${arr[$i]}"
done
```

## 实用技巧

```bash
# 字符串转数组（按空格拆分）
str="a b c"
arr=($str)

# 下标可以用变量
i=2; echo ${arr[$i]}
```
