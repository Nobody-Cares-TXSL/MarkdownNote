# os

> os 是 Python 标准库中的操作系统接口模块，提供文件、目录操作、环境变量等功能。

## 模块组成

| 子模块/功能 | 说明 | 常见用途 |
|------------|------|---------|
| `os` | 目录操作、环境变量、进程管理 | 创建目录、获取环境变量 |
| `os.path` | 路径操作 | 路径拼接、检查路径类型 |

### 导入

```python
import os
```

---

## os.path - 路径操作

| 函数 | 说明 | 示例 |
|------|------|------|
| `os.path.join(a, b, ...)` | 智能拼接路径 | `os.path.join("a", "b")` → `"a/b"` |
| `os.path.exists(path)` | 路径是否存在 | 返回 `bool` |
| `os.path.isfile(path)` | 是否为文件 | 返回 `bool` |
| `os.path.isdir(path)` | 是否为目录 | 返回 `bool` |
| `os.path.basename(path)` | 文件名 | `os.path.basename("a/b.txt")` → `"b.txt"` |
| `os.path.dirname(path)` | 目录路径 | `os.path.dirname("a/b.txt")` → `"a"` |
| `os.path.abspath(path)` | 绝对路径 | 返回完整路径 |
| `os.path.splitext(path)` | 分离扩展名 | `os.path.splitext("a.txt")` → `("a", ".txt")` |

---

## os - 目录操作

### 常用函数

| 函数 | 说明 | 示例 |
|------|------|------|
| `os.makedirs(path)` | 创建多级目录 | `os.makedirs("a/b/c")` |
| `os.listdir(path)` | 列出目录内容 | 返回文件名列表 |
| `os.getcwd()` | 获取当前工作目录 | 返回路径字符串 |
| `os.chdir(path)` | 切换工作目录 | 无返回值 |
| `os.environ` | 环境变量字典 | `os.environ["PATH"]` |

### os.makedirs() - 创建目录

| 参数 | 说明 |
|------|------|
| `path` | 目录路径 |
| `exist_ok=True` | 目录已存在时不报错 |

```python
os.makedirs(path, exist_ok=True)
```

### 使用示例

```python
import os

# 1. 路径拼接（跨平台兼容）
skill_path = os.path.join("skills", "my-skill")
script_path = os.path.join(skill_path, "scripts", "wrapper.py")
# skills/my-skill/scripts/wrapper.py

# 2. 创建目录结构
os.makedirs(os.path.join(skill_path, "scripts"), exist_ok=True)
os.makedirs(os.path.join(skill_path, "references"), exist_ok=True)

# 3. 检查路径存在
if os.path.exists("config.json"):
    print("配置文件存在")

# 4. 获取文件名和扩展名
filepath = "/path/to/file.txt"
name = os.path.basename(filepath)    # file.txt
dirname = os.path.dirname(filepath)  # /path/to
name_without_ext, ext = os.path.splitext(filepath)  # ('/path/to/file', '.txt')
```
