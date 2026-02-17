# claude 配置和常用操作

> `Claude Code`是Anthropic 官方的 Claude AI 命令行工具，支持代码编写、调试、文件操作等功能。

## 安装

使用 Windows 包管理器 winget 安装：

```powershell
winget install Anthropic.ClaudeCode
```

## 更新

### 方式一：内置更新（推荐）

```powershell
claude update
```

### 方式二：通过 winget 更新

```powershell
winget upgrade Anthropic.ClaudeCode
```

## 配置

### 1. 设置环境变量

打开 PowerShell，执行以下命令：

```powershell
# API 密钥（替换为你的密钥）
setx ANTHROPIC_AUTH_TOKEN "your_api_key_here"

# API 基础 URL（使用智谱 AI 的 API）
setx ANTHROPIC_BASE_URL "https://open.bigmodel.cn/api/anthropic"

# 禁用非必要网络请求（推荐）
setx CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC 1
```

**环境变量说明：**

| 环境变量 | 说明 |
|---------|------|
| `ANTHROPIC_AUTH_TOKEN` | API 认证密钥 |
| `ANTHROPIC_BASE_URL` | API 服务地址 |
| `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` | 禁用遥测等非必要请求 |

> 设置环境变量后需要重启终端才能生效

### 2. 配置模型映射

Claude 配置文件默认位置：

```
C:\Users\<用户名>\.claude\settings.json
```

编辑 `settings.json`（如果不存在则创建）：

```json
{
    "env": {
        "ANTHROPIC_DEFAULT_HAIKU_MODEL": "glm-4.5-air",
        "ANTHROPIC_DEFAULT_SONNET_MODEL": "glm-4.7",
        "ANTHROPIC_DEFAULT_OPUS_MODEL": "glm-4.7"
    }
}
```

## 常用操作

### 常用命令

| 命令 | 说明 |
|------|------|
| `/clear` | 清空对话历史 |
| `/init` | 生成/更新 CLAUDE.md，让 Claude 了解项目 |
| `/model` | 切换模型 |
| `/rename` | 给当前会话命名 |
| `/compact` | 压缩/总结当前上下文 |
| `/export` | 导出会话为 Markdown |
| `/context` | 查看上下文/Token 占用 |
| `/stats` | 查看使用统计 |
| `/insights` | 生成 Claude Code 的洞察 HTML 报告 |
| `/plugin marketplace list` | 列出所有已添加的市场 |
| `/terminal-setup` | 启用便捷的终端集成功能，例如支持按 Shift + Enter 换行 |
| `@file` | 快速把文件/目录拉进上下文 |

### 常用 CLI 参数

| 参数 | 说明 |
|------|------|
| `claude -c` | 继续当前目录下最近一次会话 |
| `claude -r "session"` | 通过会话名/ID 恢复会话 |
| `claude -p "query"` | Print/Headless 模式：输出到 stdout 后退出 |
| `cat file &#124; claude -p "query"` | 处理管道输入 |
| `claude -p "query" --output-format json &gt; output.json` | JSON 格式输出到文件 |

### claude code 快捷键

| 快捷键 | 用途 |
|--------|------|
| `!<command>` | 直接执行 Bash 命令并把输出注入上下文 |
| `Esc` | 中断当前思考/执行 |
| `Esc Esc` | 回到更早的检查点（rewind） |
| `Ctrl+G` | 在默认文本编辑器中编辑当前输入 |
| `Ctrl+R` | 反向搜索历史提示词 |
| `Ctrl+S` | 暂存当前提示词草稿 |
| `Ctrl+U` | 删除整行 |
| `Shift+Tab` | 循环切换权限模式（含 Plan Mode） |
| `Tab / Enter` | 接受提示词建议 |

## git bash 终端启动claude code
### 修改PowerShell配置文件
```powershell
# 打开$PROFILE 文件
notepad $PROFILE

# 确认 bash 执行文件路径
Get-Command git | Select-Object Source
# 比如输出: D:\Git\Git\cmd\git.exe
# 则可确认bash的执行文件在: D:\Git\Git\bin\bash.exe

# 在 $PROFILE 文件的开头中添加以下内容
# 告诉 Claude Code 你的 Bash 在哪里
$env:CLAUDE_CODE_GIT_BASH_PATH = "D:\Git\Git\bin\bash.exe"

# 重新加载配置
. $PROFILE
```