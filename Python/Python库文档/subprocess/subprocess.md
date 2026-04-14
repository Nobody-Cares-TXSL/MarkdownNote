# subprocess

> subprocess 是 Python 标准库中的进程管理模块，用于创建新进程、连接输入/输出管道、获取返回码。

## 模块组成

| 函数/类 | 说明 | 示例 |
|---------|------|------|
| `subprocess.run()` | 运行命令并等待完成 | `subprocess.run(["ls", "-l"])` |
| `CompletedProcess` | 返回结果类 | 包含 returncode、stdout 等 |
| `CalledProcessError` | 命令失败异常 | 退出码非零时抛出 |

### 导入

```python
import subprocess
```

---

## subprocess.run()

> run() 是 subprocess 的核心函数，用于执行外部命令并获取结果。

### 常用参数

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `cmd` | 命令列表或字符串 | 必填 |
| `check` | 为 True 时，非零退出码抛出异常 | `False` |
| `capture_output` | 捕获 stdout 和 stderr | `False` |
| `text` | 以文本模式处理输出（而非字节） | `False` |
| `shell` | 是否通过 shell 执行命令 | `False` |
| `timeout` | 超时时间（秒） | `None` |
| `cwd` | 工作目录 | `None` |
| `env` | 环境变量字典 | `None` |

### 返回值 CompletedProcess

| 属性 | 说明 |
|------|------|
| `.returncode` | 退出码（0 表示成功） |
| `.stdout` | 标准输出内容 |
| `.stderr` | 标准错误内容 |
| `.args` | 执行的参数 |

### 异常

| 异常 | 触发条件 |
|------|----------|
| `CalledProcessError` | `check=True` 且退出码非零 |
| `FileNotFoundError` | 命令不存在 |
| `TimeoutExpired` | 执行超时 |

### 使用示例

```python
import subprocess

# 1. 基本运行
result = subprocess.run(["python", "script.py"])

# 2. 检查退出码
result = subprocess.run(["python", "script.py"], check=True)
# 如果脚本返回非零，抛出 CalledProcessError

# 3. 捕获输出
result = subprocess.run(
    ["ls", "-l"],
    capture_output=True,
    text=True
)
print(result.stdout)  # 标准输出
print(result.stderr)  # 标准错误

# 4. 传递参数
cmd = [sys.executable, "script.py", "arg1", "arg2"]
subprocess.run(cmd)

# 5. 错误处理
try:
    result = subprocess.run(cmd, check=True)
except subprocess.CalledProcessError as e:
    print(f"命令失败，退出码: {e.returncode}")
except FileNotFoundError:
    print("找不到Python解释器")
```

### CalledProcessError 异常

```python
try:
    subprocess.run(["false"], check=True)
except subprocess.CalledProcessError as e:
    print(f"命令: {e.cmd}")
    print(f"退出码: {e.returncode}")
    print(f"输出: {e.output}")
    print(f"标准错误: {e.stderr}")
```
