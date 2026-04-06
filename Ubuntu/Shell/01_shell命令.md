# Shell 命令

## echo — 文本输出
> echo 会自动换行，除非使用 `-n` 选项。

### 基本语法

```bash
echo [选项] [字符串]
```

### 常用选项

| 选项 | 说明 | 示例 |
|------|------|------|
| `-n` | 不换行输出 | `echo -n "Loading..."` |
| `-e` | 启用转义字符解释 | `echo -e "First\nSecond"` |

### 转义序列

| 序列 | 说明 |
|------|------|
| `\n` | 换行 |
| `\t` | 水平制表符 |
| `\b` | 退格 |
| `\r` | 回车 |
| `\\` | 反斜杠本身 |

### 核心用法

```bash
# 输出变量（建议用双引号包裹）
name="Linux"
echo "Welcome, $name!"

# 命令替换
echo "Today is $(date)"

# 输出到文件
echo "content" > file.txt    # 覆盖
echo "content" >> file.txt   # 追加
```

### 彩色输出（ANSI 转义码）

```bash
echo -e "\033[31mRed Text\033[0m"              # 红色文字
echo -e "\033[42;31mGreen BG + Red Text\033[0m" # 绿底红字
```

颜色代码：前景 `30-37`（黑红绿黄蓝紫青白），背景 `40-47`，`\033[0m` 重置。

### 注意事项

- 变量含空格时**必须双引号**包裹：`echo "$var"`（不是 `echo $var`）
- 不同 Shell 的 `echo` 实现有差异，复杂场景建议用 `printf`

---

## printf — 格式化输出

### 基本语法

```bash
printf format-string [arguments...]
```

与 `echo` 不同：**不会自动换行**，需手动加 `\n`；可移植性更好（POSIX 标准）。

### 格式说明符

| 符号 | 说明 | 示例 |
|------|------|------|
| `%s` | 字符串 | `printf "%s\n" "hello"` |
| `%d` | 十进制整数 | `printf "%d\n" 255` |
| `%f` | 浮点数 | `printf "%.2f\n" 3.14` |
| `%c` | 单个字符 | `printf "%c\n" "A"` |
| `%x` | 十六进制 | `printf "%x\n" 255` → ff |
| `%o` | 八进制 | `printf "%o\n" 255` → 377 |
| `%e` | 科学计数法 | `printf "%e\n" 3.14` |

### 格式化控制

```bash
# 宽度与对齐（负号左对齐）
printf "|%10s|\n" "right"     # |     right|  右对齐，宽10
printf "|%-10s|\n" "left"     # |left      |  左对齐，宽10

# 前导零 & 精度
printf "%04d\n" 23            # 0023
printf "%.2f\n" 3.14159      # 3.14
```

### 多参数循环

格式字符串不足时，会循环复用格式：

```bash
printf "%-10s %5d %8.2f\n" "Apple" 5 2.5 "Orange" 3 1.75
# Apple         5     2.50
# Orange        3     1.75
```

---

## test — 条件判断
> Shell 内置条件判断工具，返回 true/false，常与 `if` 配合使用。

### 语法

```bash
test EXPRESSION
# 等价写法（方括号内侧必须有空格）
[ EXPRESSION ]
```

### 数值比较

| 操作符 | 说明 | 示例 |
|--------|------|------|
| `-eq` | 等于 | `[ "$a" -eq "$b" ]` |
| `-ne` | 不等于 | `[ "$a" -ne "$b" ]` |
| `-gt` | 大于 | `[ "$a" -gt "$b" ]` |
| `-ge` | 大于等于 | `[ "$a" -ge "$b" ]` |
| `-lt` | 小于 | `[ "$a" -lt "$b" ]` |
| `-le` | 小于等于 | `[ "$a" -le "$b" ]` |

### 字符串比较

| 操作符 | 说明 | 示例 |
|--------|------|------|
| `=` | 相等 | `[ "$a" = "$b" ]` |
| `!=` | 不等 | `[ "$a" != "$b" ]` |
| `-z` | 为空 | `[ -z "$var" ]` |
| `-n` | 非空 | `[ -n "$var" ]` |

### 文件测试

| 操作符 | 说明 | 示例 |
|--------|------|------|
| `-e` | 存在 | `[ -e file.txt ]` |
| `-f` | 普通文件 | `[ -f file.txt ]` |
| `-d` | 目录 | `[ -d /path ]` |
| `-r` | 可读 | `[ -r file.txt ]` |
| `-w` | 可写 | `[ -w file.txt ]` |
| `-x` | 可执行 | `[ -x script.sh ]` |
| `-s` | 大小 > 0 | `[ -s logfile ]` |
| `-L` | 符号链接 | `[ -L symlink ]` |

### 逻辑组合

| 操作符 | 说明 | 示例 |
|--------|------|------|
| `!` | 非 | `[ ! -f "$file" ]` |
| `-a` | 与 | `[ $a -eq 1 -a $b -eq 2 ]` |
| `-o` | 或 | `[ $a -eq 1 -o $b -eq 2 ]` |

### 高级：`[[ ]]` 和 `(( ))`

```bash
# [[ ]] 支持模式匹配和正则
[[ "$file" == *.log ]]         # 通配符匹配
[[ "$var" =~ ^[0-9]+$ ]]       # 正则匹配

# (( )) 专为数值比较
(( count > 10 ))               # 比 [ $count -gt 10 ] 更直观
```

---

## basename / dirname — 路径拆分

```bash
f="/home/duan/project/docs/readme.md"

basename "$f"     # readme.md      — 提取文件名
dirname "$f"      # /home/duan/project/docs  — 提取目录部分
```

- `basename` — 从路径中提取文件名，去掉目录前缀
- `dirname` — 提取目录部分，去掉文件名
- 常用于日志输出或显示时只保留文件名，保持简洁

```bash
DIR="$(cd "$(dirname "$0")" && pwd)"
```

### 逐层拆解

| 层级 | 代码 | 作用 |
|------|------|------|
| 1 | `$0` | 当前脚本路径（可能是相对路径，如 `./scripts/setup.sh`） |
| 2 | `dirname "$0"` | 提取目录部分（`./scripts`），文件名时返回 `.` |
| 3 | `cd "$(dirname "$0")"` | 进入脚本所在目录 |
| 4 | `&& pwd` | cd 成功后获取绝对路径 |

### 效果

`DIR` 获得脚本所在目录的**绝对路径**，无论从哪里运行脚本。

### 要点

- 获取"脚本自身所在目录"的惯用模式，几乎所有健壮脚本都会用到
- 外层双引号和 `$()` 可安全嵌套——每个 `$()` 开启新的引用上下文
- 比 `readlink -f` 兼容性更好，不依赖 GNU coreutils

---

## shopt -s nullglob — 通配符无匹配时返回空

```bash
shopt -s nullglob   # 开启
shopt -u nullglob   # 关闭
```

**作用**：通配符匹配不到文件时返回**空**，而非保留原始模式字符串。

```bash
# 目录下没有 .xyz 文件时
echo *.xyz          # 默认输出: *.xyz（原样返回）
shopt -s nullglob
echo *.xyz          # 输出: （空）
```

### 典型场景

```bash
shopt -s nullglob
for f in /var/log/*.log; do
    gzip "$f"
done
# 无 .log 文件时不执行循环体；不开 nullglob 则会尝试 gzip 字面量 "*.log"
```

### 要点

- 仅 bash 可用，POSIX sh 无 `shopt`
- 建议用完即关 `shopt -u nullglob`，避免影响后续逻辑

---

## command — 调用真实命令

Shell 内建命令，用于**绕过函数和别名**，直接调用真实的命令。

### `-v` 选项：检测命令是否存在

不执行命令，返回命令的路径或定义：

| 情况 | 输出 | 退出码 |
|------|------|--------|
| 外部命令 | `/usr/bin/fd` | 0 |
| 内建命令 | 命令名本身 | 0 |
| 别名/函数 | 别名/函数定义 | 0 |
| 未找到 | 无输出 | 1 |

```bash
command -v ls       # /usr/bin/ls
command -v cd       # cd
command -v ll       # alias ll='ls -l'
command -v foobar   # （无输出，退出码 1）
```

### 不带 `-v`：绕过函数/别名

```bash
# 假设定义了函数: ls() { echo "fake"; }
ls              # 输出 fake（调用函数）
command ls      # 输出真正的目录列表（调用 /usr/bin/ls）
```

### 典型用法

```bash
# 检测依赖是否存在（比 which 更可靠，POSIX 标准）
command -v fd && fd --version 2>/dev/null
```
