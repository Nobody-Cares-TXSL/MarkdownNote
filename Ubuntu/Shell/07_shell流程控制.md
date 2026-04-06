# Shell 流程控制

> Shell 流程控制分支**不可为空**（else 下无语句就别写 else）。

---

## if / elif / else

### 基本语法

```bash
if condition; then
    commands
elif condition2; then
    commands
else
    commands
fi
```

### 数值判断两种写法

```bash
# [ ] 中用 -gt / -lt
if [ "$a" -gt "$b" ]; then echo "a > b"; fi

# (( )) 中直接用 > / <
if (( a > b )); then echo "a > b"; fi
```

### 示例

```bash
a=10
b=20

if [ "$a" == "$b" ]; then
    echo "相等"
elif [ "$a" -gt "$b" ]; then
    echo "a 大于 b"
else
    echo "a 小于 b"
fi
# 输出: a 小于 b
```

---

## for 循环

### 遍历列表

```bash
for var in item1 item2 item3; do
    echo "$var"
done
```

### C 风格（Bash 专有，dash 不支持）

```bash
for (( i=1; i<=5; i++ )); do
    echo "$i"
done
```

### 常用变体

```bash
for f in *.txt; do echo "$f"; done            # 遍历文件
for i in $(seq 1 10); do echo "$i"; done      # seq 生成数字序列
```

### 条件过滤（循环中跳过匹配项）

```bash
orig_files=()
for f in "${files[@]}"; do
    [[ "$f" == *_compressed* ]] && continue   # 跳过含 _compressed 的文件
    [[ "$f" == *.bak ]] && continue            # 跳过备份文件
    orig_files+=("$f")
done
```

- `[[ "$f" == pattern ]]` — 通配符匹配，`==` 在 `[[ ]]` 中支持模式
- `&& continue` — 匹配则跳过，简洁的单行写法

---

## while 循环

### 基本用法

```bash
int=1
while (( int <= 5 )); do
    echo $int
    (( int++ ))
done
```

### 读取键盘输入（Ctrl-D 结束）

```bash
while read line; do
    echo "$line"
done
```

### 无限循环（三种写法）

```bash
while :; do commands; done         # 推荐
while true; do commands; done
for (( ; ; )); do commands; done
```

---

## until 循环

> 与 while 相反：条件为 **false** 时执行，**true** 时停止。一般优先用 while。

```bash
a=0
until (( a >= 10 )); do
    echo $a
    (( a++ ))
done
```

---

## case ... esac

> 类似 switch-case，每个分支以 `)` 开始，`;;` 结束（相当于 break）。

### 基本语法

```bash
case "$var" in
    pattern1)
        commands
        ;;
    pattern2|pattern3)          # | 组合多个模式
        commands
        ;;
    *)                          # 默认分支
        commands
        ;;
esac
```

### 模式匹配

```bash
case "$file" in
    *.txt)   echo "文本文件" ;;
    *.sh)    echo "脚本文件" ;;
    [0-9]*)  echo "以数字开头" ;;
    *)       echo "其他" ;;
esac
```

---

## break 与 continue

| 命令 | 作用 | 示例 |
|------|------|------|
| `break` | 跳出**所有**循环 | `break` / `break 2`（跳出两层） |
| `continue` | 跳过**当前**循环，进入下一次 | `continue` / `continue 2` |

```bash
# break：输入非 1-5 则退出
while :; do
    read num
    case $num in
        [1-5]) echo "OK: $num" ;;
        *)     echo "退出"; break ;;
    esac
done

# continue：跳过奇数
for i in $(seq 1 10); do
    (( i % 2 )) && continue
    echo "$i"    # 只输出偶数
done
```
