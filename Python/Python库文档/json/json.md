# json

> json是Python标准库中的JSON数据处理模块，用于JSON与Python对象之间的转换。

### 导入

```python
import json
```

### 核心函数

| 函数 | 输入 | 输出 | 用途 |
|------|------|------|------|
| `json.loads(s)` | JSON字符串 | Python对象 | 解析JSON字符串 |
| `json.load(f)` | 文件对象 | Python对象 | 从文件读取JSON |
| `json.dumps(obj)` | Python对象 | JSON字符串 | 转换为JSON字符串 |
| `json.dump(obj, f)` | Python对象 + 文件 | 写入文件 | 写入JSON到文件 |

### dumps() 参数

```python
json_string = json.dumps(
    obj,
    indent=2,           # 缩进空格数，美化输出
    ensure_ascii=False, # 允许非ASCII字符（中文）
    sort_keys=True      # 按键名排序
)
```

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `indent` | 缩进空格数，`None` 表示压缩 | `None` |
| `ensure_ascii` | `False` 时保留中文等字符 | `True` |
| `sort_keys` | 是否按键名排序 | `False` |

### 使用示例

```python
import json

# 1. Python对象 → JSON字符串
data = {"name": "测试", "count": 42}
json_str = json.dumps(data, ensure_ascii=False, indent=2)
print(json_str)
# 输出:
# {
#   "name": "测试",
#   "count": 42
# }

# 2. JSON字符串 → Python对象
json_str = '{"name": "repo", "url": "https://github.com/user/repo"}'
data = json.loads(json_str)
print(data['name'])  # repo

# 3. 从文件读取
with open('config.json', 'r', encoding='utf-8') as f:
    config = json.load(f)

# 4. 写入文件
data = {"version": "1.0.0", "files": ["a.py", "b.py"]}
with open('output.json', 'w', encoding='utf-8') as f:
    json.dump(data, f, indent=2, ensure_ascii=False)
```

### 类型映射

| JSON类型 | Python类型 |
|----------|------------|
| object | dict |
| array | list |
| string | str |
| number (int) | int |
| number (float) | float |
| true/false | bool |
| null | None |
