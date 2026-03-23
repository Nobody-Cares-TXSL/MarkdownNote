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

### 快速命令

| 快捷键 | 描述 | 注释 |
|--------|------|------|
| `/` 在开始 | 命令或 skill | 参考内置命令和 skills |
| `!` 在开始 | Bash 模式 | 直接运行命令并将输出添加到会话 |
| `@` | 文件路径提及 | 触发文件路径自动完成 |

### 常用命令

| 命令 | 说明 |
|------|------|
| `/clear` | 清空对话历史 |
| `/init` | 生成/更新 `CLAUDE.md`，让 Claude 了解项目 |
| `/model` | 切换模型 |
| `/rename [name]` | 给当前会话命名 |
| `/resume [session]` | 按 ID 或名称恢复对话 |
| `/compact` | 压缩/总结当前上下文 |
| `/context` | 查看上下文/Token 占用 |
| `/stats` | 查看使用统计 |
| `/diff` | 交互式差异查看器 |
| `/copy` | 复制最新的输出内容到剪贴板 |
| `/export [filename]` | 导出会话 |
| `/insights` | 生成 Claude Code 的洞察 HTML 报告 |
| `/plugin marketplace list` | 列出所有已添加的市场 |
| `/terminal-setup` | 启用便捷的终端集成功能，例如支持按 Shift + Enter 换行 |
| `/memory` | 查看/管理持久内存 |
| `/btw` | 快速问题，不占用上下文 |
| `/add-dir <path>` | 将新的工作目录添加到当前会话 |
| `/config` | 配置输出样式、模型、权限等 |
| `/fork [name]` | 创建当前会话的分支会话 |

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
| `Alt+T` | 思考模式开关 |
| `Shift+Tab` | 循环切换权限模式（含 Plan Mode） |
| `Tab / Enter` | 接受提示词建议 |

## 上下文成本

| 功能 | 何时加载 | 加载内容 | 上下文成本 |
|------|----------|----------|-----------|
| CLAUDE.md | 会话开始 | 完整内容 | 每个请求 |
| Skills | 会话开始 + 使用时 | 启动时的描述，使用时的完整内容 | 低（每个请求的描述）* |
| MCP 服务器 | 会话开始 | 所有工具定义和 JSON 架构 | 每个请求 |
| Subagents | 生成时 | 具有指定 skills 的新鲜上下文 | 与主会话隔离 |
| Hooks | 触发时 | 无（外部运行） | 零，除非 hook 返回添加为消息的其他上下文 |

## 输出样式

输出样式将 Claude Code 适配用于不同用途，修改系统提示以改变响应方式。

### 内置样式

| 样式 | 说明 | 适用场景 |
|------|------|---------|
| **Default** | 默认软件工程模式 | 高效完成编码任务 |
| **Explanatory** | 提供教育性 insights | 理解实现选择和代码库模式 |
| **Learning** | 边学边做，添加 `TODO(human)` | 主动学习、协作编程 |

### 更改样式

```bash
/config  # 菜单选择
```

或编辑 `.claude/settings.local.json`：

```json
{
  "outputStyle": "Explanatory"
}
```

> 更改需重启会话生效，以便 prompt caching 降低延迟

### 自定义样式

创建 Markdown 文件，位置：
- 用户级：`~/.claude/output-styles/<name>.md`
- 项目级：`.claude/output-styles/<name>.md`

```markdown
---
name: My Style
description: 简要描述
keep-coding-instructions: false
---

# 自定义指令

[你的自定义说明...]
```

**Frontmatter 字段：**

| 字段 | 说明 | 默认值 |
|------|------|--------|
| `name` | 输出样式的名称，如果不是文件名 | 从文件名继承 |
| `description` | 输出样式的描述，在 `/config` 选择器中显示 | 无 |
| `keep-coding-instructions` | 是否保留 Claude Code 系统提示中与编码相关的部分 | false |

### 功能对比

| 功能 | 作用 | 范围 |
|------|------|------|
| **输出样式** | 修改系统提示，改变响应方式 | 全局始终生效 |
| **CLAUDE.md** | 项目级用户消息 | 当前项目 |
| **Skills** | 可调用任务提示 | 按需加载 |
| **Agents** | 特定任务处理器 | 按需调用 |

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