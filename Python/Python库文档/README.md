# Python库文档

| 库 | 直观理解 | 一句话 |
|----|----------|--------|
| [sys](./sys/sys.md) | 📋 读取命令行参数 | "用户运行脚本时传了什么参数？用 sys.argv 读取" |
| [argparse](./argparse/argparse.md) | 🎯 解析复杂参数 | "要做专业 CLI 工具，支持 --help、--version？用 argparse" |
| [subprocess](./subprocess/subprocess.md) | 🚀 调用外部命令 | "要在 Python 里运行 git、npm 等命令？用 subprocess" |
| [os](./os/os.md) | 🖥️ 操作系统接口 | "要创建目录、拼接路径？用 os" |
| [pathlib](./pathlib/pathlib.md) | 📁 操作文件路径 | "要拼路径、读文件、创建目录？用 pathlib 比 os 更简单" |
| [urllib](./urllib/urllib.md) | 🌐 发送 HTTP 请求 | "要下载文件、调用 API？用 urllib.request" |
| [json](./json/json.md) | 📦 处理 JSON 数据 | "要读写 JSON 配置文件？用 json" |
| [re](./re/re.md) | 🔍 正则表达式 | "要搜索、替换、提取文本模式？用 re" |
| [datetime](./datetime/datetime.md) | 🕐 日期时间处理 | "要获取当前时间、格式化日期？用 datetime" |

## 文档填充模版
模版可以进行适度优化

```markdown
# [库名称]

> 库的直观理解描述。

## 模块组成

| 类 | 说明 | 示例 |
|-----|------|------|
| `类名` | 功能说明 | 示例值 |

---

## [核心类]

> 类的用途说明。

### 导入
```python
类的导入方式
```

### 常用方法

| 方法/属性 | 说明 | 示例 |
|----------|------|------|
| `.method()` | 功能描述 | 返回值 |
| `.attr` | 属性说明 | 值类型 |

### 辅助表格（如格式符、选项等）

| 项目 | 说明 | 示例 |
|-----|------|------|
| ... | ... | ... |

### 使用示例

```python
# 代码注释说明
code_here
```

---
```