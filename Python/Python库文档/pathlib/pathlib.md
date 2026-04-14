# pathlib

> pathlib 是 Python 标准库中的面向对象路径操作模块，提供跨平台兼容的文件系统路径操作。

## 模块组成

| 类 | 说明 | 示例 |
|-----|------|------|
| `Path` | 文件系统路径（具体路径） | `Path("/home/user/file.txt")` |
| `PurePath` | 纯路径操作（不访问文件系统） | 用于路径处理逻辑 |

### 导入

```python
import pathlib
```

---

## Path 类

> Path 类是 pathlib 的核心，提供文件路径的创建、拼接、检查、读写等操作。

### 导入

```python
from pathlib import Path
```

### 创建路径对象

```python
# 绝对路径
p = Path("C:/Users/user/file.txt")

# 相对路径
p = Path("data/config.json")

# 当前文件所在目录
p = Path(__file__).parent  # 获取父目录
```

### 路径拼接 `/`

使用 `/` 运算符拼接路径，跨平台兼容。

```python
base = Path("/home/user")
full = base / "documents" / "file.txt"
# /home/user/documents/file.txt

# 链式拼接
script_dir = Path(__file__).parent.parent / "references" / "Script"
```

### 常用方法

| 方法/属性 | 说明 | 返回类型 |
|----------|------|----------|
| `.exists()` | 路径是否存在 | `bool` |
| `.is_file()` | 是否为文件 | `bool` |
| `.is_dir()` | 是否为目录 | `bool` |
| `.parent` | 父目录 | `Path` |
| `.name` | 文件名（含扩展名） | `str` |
| `.stem` | 文件名（不含扩展名） | `str` |
| `.suffix` | 扩展名 | `str` |
| `.stat()` | 文件状态信息 | `os.stat_result` |
| `.read_text()` | 读取文本内容 | `str` |
| `.write_text()` | 写入文本内容 | `None` |
| `.mkdir()` | 创建目录 | `None` |
| `.resolve()` | 解析为绝对路径 | `Path` |
| `.absolute()` | 获取绝对路径 | `Path` |

### 路径组件

| 属性 | 说明 | 示例 |
|------|------|------|
| `.parent` | 父路径 | `Path("C:/Users/user/docs")` |
| `.name` | 文件名（含扩展名） | `"file.txt"` |
| `.stem` | 文件名（不含扩展名） | `"file"` |
| `.suffix` | 扩展名 | `".txt"` |
| `.anchor` | 根目录 | `"C:/"` |

```python
p = Path("C:/Users/user/docs/file.txt")
p.parent   # Path("C:/Users/user/docs")
p.name     # "file.txt"
p.stem     # "file"
p.suffix   # ".txt"
```

### mkdir() 参数

| 参数 | 说明 |
|------|------|
| `parents=True` | 创建父目录 |
| `exist_ok=True` | 目录已存在不报错 |

### 使用示例

```python
from pathlib import Path

# 1. 路径拼接
script_dir = Path(__file__).parent / "scripts"
config_path = script_dir / "config.json"

# 2. 检查文件/目录存在
if config_path.exists():
    if config_path.is_file():
        print("是文件")
    elif config_path.is_dir():
        print("是目录")

# 3. 读写文件
content = config_path.read_text(encoding="utf-8")
config_path.write_text("新内容", encoding="utf-8")

# 4. 创建目录
Path("output/results").mkdir(parents=True, exist_ok=True)

# 5. 遍历目录（glob）
for file in Path("data").glob("*.txt"):
    print(file.name)

# 6. 递归遍历（rglob）
for py_file in Path(".").rglob("*.py"):
    print(py_file)

# 7. 与字符串转换
path_str = str(Path("/home/user/file.txt"))
cmd = [sys.executable, str(script_path)]
```
