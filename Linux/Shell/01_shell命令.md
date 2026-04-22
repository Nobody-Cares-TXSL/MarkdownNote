# Shell 命令

## #!/usr/bin/env bash — 可移植的 shebang

```bash
#!/usr/bin/env bash
```

通过 `env` 在 `$PATH` 中查找 `bash`，而非硬编码路径（如 `/bin/bash`），确保在 bash 安装于非标准路径（如 `/usr/local/bin/bash`）的系统中也能正常运行。

### 与 `#!/bin/bash` 的区别

| 写法 | 查找方式 | 可移植性 |
|------|---------|---------|
| `#!/bin/bash` | 直接指向 `/bin/bash` | 仅限 bash 装在 `/bin` 的系统 |
| `#!/usr/bin/env bash` | 通过 `env` 在 `$PATH` 中查找 | 跨平台可移植 |

### 要点

- 仅在使用 bash 特性时用 `bash`；纯 POSIX 脚本应写 `#!/usr/bin/env sh`
- 脚本文件需要执行权限才能直接运行：`chmod +x script.sh`

---

## set -euo pipefail — 严格模式三件套

```bash
set -euo pipefail
```

### 选项说明

| 选项 | 全称 | 作用 | 示例 |
|------|------|------|------|
| `-e` | errexit | 任何命令失败立即退出 | `false` → 脚本终止 |
| `-u` | nounset | 引用未定义变量时报错 | `echo $UNDEFINED` → 报错退出 |
| `-o pipefail` | — | 管道中任一环节失败，整个管道算失败 | `false \| true` → 退出码 1 |

### 各选项详解

```bash
# -e：命令失败即退出（捕获意外错误）
set -e
cp missing_file /tmp/    # cp 失败，脚本立即终止，不继续执行后续命令

# -u：防止拼写错误导致静默产生空值
set -u
echo "$HOM"              # 变量名拼错，报错：HOM: unbound variable

# -o pipefail：管道不再只看最后一个命令的退出码
set -o pipefail
grep "pattern" file.txt | sort | head -5
# grep 没匹配到时返回 1，整个管道算失败（默认只看 head 的退出码 0）
```

### 要点

- 三者组合使用，大幅提升脚本的健壮性
- `-e` 不影响 `if`、`while` 条件中的命令，也不影响 `&&` / `||` 右侧
- 需要容忍失败时，可用 `cmd || true` 临时抑制

---

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

---

## grep — 文本搜索

基于正则表达式搜索文件中的匹配行。

### 基本语法

```bash
grep [选项] 模式 [文件...]
```

### 常用选项

| 选项 | 说明 | 示例 |
|------|------|------|
| `-n` | 显示行号 | `grep -n "error" log.txt` → `42:error occurred` |
| `-i` | 忽略大小写 | `grep -i "Error" log.txt` |
| `-r` | 递归搜索目录 | `grep -r "TODO" src/` |
| `-v` | 反向匹配（排除） | `grep -v "^#" config.conf` |
| `-c` | 只输出匹配行数 | `grep -c "fail" log.txt` |
| `-l` | 只输出匹配的文件名 | `grep -rl "pattern" .` |
| `-E` | 扩展正则（ERE） | `grep -E "cat\|dog" file` |
| `-o` | 只输出匹配部分 | `grep -o "[0-9]+" file` |
| `-A N` | 显示匹配行后 N 行 | `grep -A 3 "error" log.txt` |
| `-B N` | 显示匹配行前 N 行 | `grep -B 3 "error" log.txt` |
| `-C N` | 显示匹配行前后各 N 行 | `grep -C 3 "error" log.txt` |

### 正则表达式速查

| 符号 | 说明 | 示例 |
|------|------|------|
| `^` | 行首锚点 | `^## 语义分析` — 匹配以 `## 语义分析` 开头的行 |
| `$` | 行尾锚点 | `^$` — 匹配空行 |
| `.` | 任意单个字符 | `a.c` 匹配 `abc`、`a1c` |
| `*` | 前一项零次或多次 | `ab*c` 匹配 `ac`、`abc`、`abbc` |
| `[]` | 字符类 | `[0-9]` 匹配任意数字 |
| `[^]` | 取反字符类 | `[^0-9]` 匹配非数字 |

### 典型用法

```bash
# 在 Markdown 文件中定位二级标题，返回行号
grep -n "^## " README.md
# 输出: 42:## 语义分析

# 搜索变量值包含空格的文件（变量必须双引号）
grep -n "keyword" "$file"

# 排除注释行和空行
grep -v "^#" config.conf | grep -v "^$"

# 递归搜索并显示文件名和行号
grep -rn "TODO" src/

# 使用扩展正则（支持 + ? | ()）
grep -E "^[0-9]{4}-[0-9]{2}-[0-9]{2}" dates.txt
```

### 要点

- `grep -n` 输出格式为 `行号:内容`，常配合 `cut -d: -f1` 提取行号
- 变量含空格时必须用**双引号**：`"$file"`（单引号不展开变量）
- `#` 在正则中是普通字符，无需转义

---

## cut — 列切割提取

从每行中按分隔符提取指定字段。

### 基本语法

```bash
cut [选项] [文件...]
```

### 常用选项

| 选项 | 说明 | 示例 |
|------|------|------|
| `-d` | 指定分隔符 | `cut -d: -f1` |
| `-f` | 指定提取的字段 | `cut -f1,3` |
| `-c` | 按字符位置提取 | `cut -c1-10` |
| `-b` | 按字节位置提取 | `cut -b1-10` |

### 字段选择语法

| 语法 | 说明 | 示例 |
|------|------|------|
| `-f1` | 第 1 列 | 提取行号 |
| `-f1,3` | 第 1 和第 3 列 | 提取姓名和邮箱 |
| `-f1-3` | 第 1 到 3 列 | 提取前3列 |
| `-f1-` | 第 1 列到最后 | 跳过分隔符后取全部 |
| `-f-3` | 第 1 列到第 3 列 | 同 `-f1-3` |

### 典型用法

```bash
# 从 grep -n 输出中提取行号
grep -n "^## 语义分析" "$file" | cut -d: -f1
# 输入: 42:## 语义分析
# 输出: 42

# 提取 /etc/passwd 中的用户名（第1列，分隔符为 :）
cut -d: -f1 /etc/passwd

# 提取每行前 10 个字符
cut -c1-10 file.txt
```

### 要点

- 默认分隔符是 **Tab**，用 `-d` 改为其他单字符
- `cut` 只支持**单字符分隔符**，多字符分隔用 `awk -F'::' '{print $1}'`
- 常与 `grep -n` 组合使用，从 `行号:内容` 中提取行号
