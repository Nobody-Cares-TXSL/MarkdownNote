# sys

> sys 是 Python 标准库中的系统相关参数和函数模块，提供访问解释器相关功能的接口。

## 模块组成

| 属性/函数 | 说明 | 示例 |
|----------|------|------|
| `sys.argv` | 命令行参数列表 | `['script.py', 'arg1']` |
| `sys.executable` | Python 解释器路径 | `C:\Python39\python.exe` |
| `sys.path` | 模块搜索路径列表 | `['', '/usr/lib/python3.9']` |
| `sys.platform` | 操作系统平台 | `'win32'`, `'linux'` |
| `sys.exit()` | 退出程序 | `sys.exit(0)` |

### 导入

```python
import sys
```

---

## sys.argv - 命令行参数

> sys.argv 是一个列表，包含命令行传递给脚本的参数。

### 常用属性

| 属性 | 说明 | 示例 |
|------|------|------|
| `sys.argv[0]` | 脚本名称 | `'script.py'` |
| `sys.argv[1:]` | 用户参数 | `['arg1', 'arg2']` |
| `len(sys.argv)` | 参数总数 | `3` |

### 使用示例

```python
import sys

# 1. 获取 Python 解释器路径
python_path = sys.executable
print(f"Python: {python_path}")

# 2. 运行脚本时确保使用正确的 Python 环境
cmd = [sys.executable, "script.py", "arg1"]
subprocess.run(cmd)

# 3. 读取命令行参数
if len(sys.argv) > 1:
    print(f"第一个参数: {sys.argv[1]}")

# 4. 退出程序
sys.exit(0)    # 正常退出
sys.exit(1)    # 错误退出
```
