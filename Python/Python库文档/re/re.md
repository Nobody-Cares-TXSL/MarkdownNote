# re

> re 是 Python 标准库中的正则表达式模块，提供字符串模式匹配、搜索、替换等功能。

## 模块组成

| 函数/类 | 说明 | 示例 |
|---------|------|------|
| `re.search()` | 搜索第一个匹配位置 | `re.search(r'\d+', s)` |
| `re.match()` | 从字符串开头匹配 | `re.match(r'^http', s)` |
| `re.findall()` | 查找所有匹配项 | `re.findall(r'\d+', s)` |
| `re.sub()` | 替换匹配内容 | `re.sub(r'\s+', '-', s)` |
| `re.compile()` | 编译正则表达式 | `pattern = re.compile(r'\d+')` |
| `Match` | 匹配结果对象 | 包含匹配信息 |

### 导入

```python
import re
```

---

## 常用函数

### search() - 搜索匹配

| 参数 | 说明 |
|------|------|
| `pattern` | 正则表达式模式 |
| `string` | 要搜索的字符串 |
| `flags` | 匹配标志（可选） |

### match() - 开头匹配

| 参数 | 说明 |
|------|------|
| `pattern` | 正则表达式模式 |
| `string` | 要匹配的字符串 |
| `flags` | 匹配标志（可选） |

### findall() - 查找所有

| 返回值 | 说明 |
|--------|------|
| `list` | 所有匹配项的列表 |

### sub() - 替换

| 参数 | 说明 |
|------|------|
| `pattern` | 正则表达式模式 |
| `repl` | 替换字符串 |
| `string` | 原字符串 |
| `count` | 替换次数（可选） |

---

## 常用模式标志

| 标志 | 说明 |
|------|------|
| `re.IGNORECASE` | 忽略大小写 |
| `re.DOTALL` | `.` 匹配包括换行符在内的所有字符 |
| `re.MULTILINE` | `^` 和 `$` 匹配每行的开头/结尾 |
| `re.VERBOSE` | 允许注释和格式化 |

---

## 常用模式语法

| 符号 | 说明 | 示例 |
|------|------|------|
| `.` | 匹配任意字符（除换行符） | `a.c` → `abc`, `a1c` |
| `^` | 匹配字符串开头 | `^http` → `https://` |
| `$` | 匹配字符串结尾 | `\.com$` → `example.com` |
| `*` | 匹配 0 次或多次 | `a*` → ``, `a`, `aa` |
| `+` | 匹配 1 次或多次 | `a+` → `a`, `aa` |
| `?` | 匹配 0 次或 1 次 | `a?` → ``, `a` |
| `\d` | 匹配数字 | `\d+` → `123` |
| `\s` | 匹配空白字符 | `\s+` → 空格、制表符 |
| `\w` | 匹配字母数字下划线 | `\w+` → `abc123` |
| `[]` | 字符集 | `[a-z]` → 小写字母 |
| `()` | 捕获组 | `(\d+)` → 提取数字 |
| `.*` | 贪婪匹配 | `a.*b` → 尽可能多 |
| `.*?` | 惰性匹配 | `a.*?b` → 尽可能少 |

---

## 使用示例

```python
import re

# 1. 搜索匹配
text = "Price: $99, Stock: 42"
match = re.search(r'\d+', text)
if match:
    print(match.group())  # 99

# 2. 查找所有数字
numbers = re.findall(r'\d+', text)
print(numbers)  # ['99', '42']

# 3. 替换
new_text = re.sub(r'\s+', '-', "hello    world")
print(new_text)  # hello-world

# 4. 编译模式（重复使用）
pattern = re.compile(r'## User-Learned.*?$', re.DOTALL)
match = pattern.search(content)

# 5. 提取捕获组
email = "user@example.com"
match = re.match(r'(\w+)@(\w+\.\w+)', email)
if match:
    print(match.group(1))  # user
    print(match.group(2))  # example.com

# 6. 贪婪 vs 惰性
text = "<div>内容1</div><div>内容2</div>"
greedy = re.findall(r'<div>.*</div>', text)       # 匹配到整个字符串
lazy = re.findall(r'<div>.*?</div>', text)        # 匹配到每个 div
```

---

## Match 对象方法

| 方法 | 说明 |
|------|------|
| `.group()` | 返回匹配的字符串 |
| `.group(n)` | 返回第 n 个捕获组 |
| `.start()` | 匹配起始位置 |
| `.end()` | 匹配结束位置 |
| `.span()` | 返回 (start, end) 元组 |

```python
match = re.search(r'(\d+)', "Version: 3.14")
if match:
    print(match.group())   # 3
    print(match.start())   # 9
    print(match.end())     # 10
    print(match.span())    # (9, 10)
```
