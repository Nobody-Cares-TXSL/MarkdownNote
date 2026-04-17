# Shell 变量

## 定义与使用

```bash
name='runoob'          # 定义不加 $，等号两侧不能有空格
echo $name             # 使用加 $
echo ${name}           # 推荐加 {}，避免歧义
```

命名规则：仅字母/数字/下划线，不能数字开头，不能用关键字。

## 只读与删除

```bash
readonly name          # 只读，不可修改
unset name             # 删除，不可再使用（不能删只读变量）
```

## 变量类型

| 类型 | 示例 |
|------|------|
| 字符串（默认） | `str='hello'` |
| 整数 | `declare -i n=42` |
| 索引数组 | `arr=(1 2 3)` |
| 关联数组 | `declare -A m; m["k"]="v"` |
| 环境变量 | `$PATH` |
| 特殊变量 | `$0` 脚本名、`$1~$n` 参数、`$#` 参数数、`$?` 上条退出状态 |

---

# Shell 字符串

## 单引号 vs 双引号

| | 单引号 | 双引号 |
|---|--------|--------|
| 变量 | 不解析 | 解析 |
| 转义 | 不支持 | 支持 |

```bash
name="runoob"
echo 'Hello ${name}'    # Hello ${name}
echo "Hello ${name}"    # Hello runoob
```

## 常用操作

```bash
str="runoob is great"
echo ${#str}            # 长度：15
echo ${str:1:4}         # 截取：unoo（从索引1取4个，索引从0开始）
echo `expr index "$str" io`  # 查找：4（i/o 先出现的位置）
```

---

# Shell 参数扩展

用 `${var#pattern}` 等语法对变量值进行截取/删除，是纯 bash 内置，无需外部命令。

## 默认值与错误检查

| 语法 | 含义 | 示例 |
|------|------|------|
| `${var:-default}` | 未设置或为空 → 返回默认值 | `echo ${name:-"guest"}` |
| `${var:=default}` | 未设置或为空 → 赋默认值 | `${name:="guest"}` → name 被赋值 |
| `${var:+alt}` | 已设置且非空 → 返回替代值 | `echo ${name:+"Hi"}` |
| `${var:?message}` | 未设置或为空 → 报错退出并打印 message | `${1:?"用法: $0 <file>"}` |

### `:?` 典型用法：参数校验

```bash
#!/usr/bin/env bash
set -euo pipefail

INPUT="${1:?"错误: 缺少必需参数 <input_file>"}"
OUTPUT="${2:-output.txt}"   # 可选参数，默认 output.txt

echo "处理 $INPUT → $OUTPUT"
```

```bash
$ ./script.sh
bash: 1: 错误: 缺少必需参数 <input_file>
```

### 要点

- `-` / `=` / `+` / `?` 四种操作符区别在于：**缺省值**、**赋值**、**替代值**、**报错**
- 加 `:` 时，空字符串也视为"未设置"；不加 `:` 仅对真正未定义的变量触发
- 与 `set -u` 搭配使用效果最好，`:?` 提供更友好的错误提示

---

## 截取与删除

### 语法总览

| 语法 | 含义 | 记忆法 |
|------|------|--------|
| `${f#pattern}` | 从**开头**删除**最短**匹配 | `#` 键盘左侧 → 开头 |
| `${f##pattern}` | 从**开头**删除**最长**匹配 | 双符号 = 贪婪 |
| `${f%pattern}` | 从**结尾**删除**最短**匹配 | `%` 键盘右侧 → 结尾 |
| `${f%%pattern}` | 从**结尾**删除**最长**匹配 | 双符号 = 贪婪 |

## 示例

```bash
f="tmp_docs_guide.md"

echo ${f#tmp_}       # → "docs_guide.md"     去掉开头第一个 tmp_
echo ${f##*_}        # → "guide.md"          去掉开头到最后一个 _（最长匹配）
echo ${f%.md}        # → "tmp_docs_guide"    去掉结尾的 .md
echo ${f%%_*}        # → "tmp"               去掉第一个 _ 及之后所有内容
```

---

# Shell 注释

```bash
# 单行注释

:<<EOF                   # 多行注释（Here 文档）
注释内容...
EOF

: '                      # 多行注释（冒号+单引号）
注释内容...
'
```
