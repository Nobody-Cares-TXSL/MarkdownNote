# Claude Code

> Anthropic 官方的 Claude AI 命令行工具，支持代码编写、调试、文件操作等功能。

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

## 插件系统：市场与插件

> 💡 **简单理解**：「市场」就像应用商店，「插件」就像商店里的 App。需要先添加商店，再从商店里安装你需要的插件。

### 第一步：添加市场

把官方插件仓库添加到本地：

```powershell
/plugin marketplace add https://github.com/anthropics/skills
```

> 本质上就是帮你自动执行了一次 git clone，把远程仓库拉取到了你本地的 `C:\Users\<用户名>\.claude\plugins\marketplaces` 目录下

执行后，插件市场会被下载到本地目录：
```
C:\Users\<你的用户名>\.claude\plugins\marketplaces\
```

### 第二步：安装插件

从已添加的市场中安装具体插件：

**命令格式：**

```powershell
/plugin install <插件名>@<市场名>
```

| 部分 | 说明 | 示例 |
|------|------|------|
| `<插件名>` | 要安装的具体插件名称 | `document-skills` |
| `<市场名>` | 插件所在的市场名称 | `anthropic-agent-skills` |

**安装示例：**

> 💡 提示：可通过下方「查看插件配置信息」章节确认市场中包含的插件和技能

```powershell
# 文档处理插件（Word、PDF、Excel 等）
/plugin install document-skills@anthropic-agent-skills

# 示例插件合集（前端设计、PPT 制作、算法艺术等）
/plugin install example-skills@anthropic-agent-skills
```

插件安装位置：
```
C:\Users\<你的用户名>\.claude\plugins\cache\
```
> 安装后，插件会被复制到 `cache` 目录，并在 Claude Code 中自动加载；你可以在任何会话中使用插件提供的功能，无需再次配置。

### 查看市场
```powershell
# 列出所有可用市场
/plugin marketplace list

# 查看已安装的插件
/plugin list
```
### 查看插件配置信息

| 文件 | 位置 | 作用 |
|------|------|------|
| `marketplace.json` | 市场仓库根目录 `/.claude-plugin/` | 定义市场名称、所有者信息，列出包含的插件及每个插件包含的技能 |
| `plugin.json` | 各插件目录 `/<插件名>/.claude-plugin/` | 定义单个插件的具体配置 |

本地路径示例：
```
# 市场配置
C:\Users\<你的用户名>\.claude\plugins\marketplaces\skills\.claude-plugin\marketplace.json

# 插件配置（如果存在）
C:\Users\<你的用户名>\.claude\plugins\marketplaces\skills\plugins\<插件名>\.claude-plugin\plugin.json
```

---

## Skill

Skill 既可以被 Claude 在合适场景自动加载，也可以由你通过 /skill-name 手动调用。

### Skills 放在哪里（作用域与优先级）

存放位置决定谁可以使用：

| 位置 | 路径 | 适用范围 |
|------|------|---------|
| 企业/托管 | 参考「托管设置」 | 组织内所有用户 |
| 个人 | `~/.claude/skills/<skill-name>/SKILL.md` | 你的所有项目 |
| 项目 | `.claude/skills/<skill-name>/SKILL.md` | 仅当前仓库 |
| 插件 | `<plugin>/skills/<skill-name>/SKILL.md` | 启用该插件的项目 |

> 同名冲突时：项目 Skill 会覆盖个人 Skill；如果同时存在 .claude/commands/<name>.md 与 .claude/skills/<name>/SKILL.md，Skill 优先。

### SKILL结构

一个 Skill 是一个目录，入口文件固定为 SKILL.md：  
每个技能遵循以下结构：

```
skill-name/
├── SKILL.md          # 必需：主技能文档，包含 YAML frontmatter
├── examples.md       # 可选：示例输出
├── references/       # 可选：包含代理在需要时可以读取的附加文档
├── scripts/          # 可选：包含代理可以运行的执行代码
├── assets/           # 可选：包含技能运行时需要的静态资源（例如HTML 模板、CSS 文件、图像、配置样板或字体）
└── templates/        # 可选：文件模板或样板代码
```
> 把大段参考资料拆到单独文件，并在 SKILL.md 里链接它们，能让上下文更“轻”，也更容易维护。

### SKILL的渐进式披露

- 元数据（~100 个符号）： name 和 description 字段在所有技能启动时加载
- 说明（建议<5000 个符号）：技能激活时加载完整的 SKILL.md 正文
- 资源（按需使用）：文件（例如 scripts/ 、 references/ 或 assets/ 中的文件）仅在需要时加载

> 保持你的主 SKILL.md 在 500 行以下。将详细的参考材料移动到单独的文件中。

### SKILL.md 文件
`SKILL.md`文件是遵循两部分结构的 markdown 文件——`Frontmatter` 和`内容`
- Frontmatter 配置 skill 如何运行（权限、模型、元数据）, 用 YAML 编写在 markdown 文件头部
- Markdown 内容：Skill 被调用时 Claude 应遵循的指令
### YAML Frontmatter（必需）

每个 `SKILL.md` 必须以 YAML frontmatter 开头：

```yaml
---
name: skill-name              # (必填)小写字母、连字符
description: 简要描述         # (必填)`[做什么] + [何时使用] + [关键触发词]`
---
```
> 其他字段根据需要填写,[Frontmatter参考](#frontmatter-字段说明)

### Frontmatter 字段说明

| 字段 | 说明 |
|-----|------|
| `name` | Skill 名称（省略则使用目录名）；只允许小写字母/数字/连字符, 不能以连字符开头或结尾（最长 64个字符） |
| `description` | 最多 1024 个字符。非空。描述技能: `[做什么] + [何时使用] + [关键触发词]` |
| `argument-hint` | 自动补全时显示的参数提示（例如 `[issue-number]`、`[file] [format]`） |
| `disable-model-invocation` | 设为 `true` 后，Claude 不会自动触发，只能由你通过 `/name` 手动触发 |
| `user-invocable` | 设为 `false` 后，从 `/` 菜单隐藏, 仅控制菜单可见性，不控制技能工具访问 |
| `allowed-tools` | 用空格分隔的已预先批准可运行的工具列表 |
| `model` | Skill 激活时使用的模型 |
| `context` | 设为 `fork` 时在隔离的子代理上下文运行 |
| `agent` | `context: fork` 时选择子代理类型（例如 Explore、Plan） |
| `hooks` | 仅对该 Skill 生效的 hooks（格式见 Hooks 文档） |
| `license` | 许可证名称或捆绑许可证文件的引用 |
| `compatibility` | 最多 500 个字符。指示环境要求（预期产品、系统包、网络访问等） |
| `metadata` | 用于附加元数据的任意键值映射 |

### 字符串替换（参数与会话 ID）

| 变量 | 含义 | 示例 | 
|------|------|------|
| `$ARGUMENTS` | 调用技能时传递的所有参数。如果内容中不存在 `$ARGUMENTS` ，Claude Code 会在技能内容的末尾追加 `ARGUMENTS: <your input>` | /review src/ → `$ARGUMENTS` 变成 src/ |
| `$ARGUMENTS[N]` | 通过 0-based 索引访问特定参数，例如 `$ARGUMENTS[0]` 表示第一个参数 | /review src/ → `$ARGUMENTS[0]` 变成 src/ |
| `$N` | `$ARGUMENTS[N]` 的简写，例如 `$0` 表示第一个参数或 `$1` 表示第二个参数 | /review src/ code → `$0` 变成 src/, `$1` 变成 code |
| `${CLAUDE_SESSION_ID}` | 当前会话 ID（便于做日志/产物命名） | log-${CLAUDE_SESSION_ID}.txt → log-abc123.txt |

### 动态上下文注入（先执行，再注入）

在 Skill 内容里使用 !`command` 占位符，Claude Code 会在把 Skill 发送给 Claude 之前执行命令，并用输出替换占位符；Claude 看到的是“已注入的数据”，不是命令本身。

```yaml
---
name: review-changes
description: 审查当前 Git 仓库的未提交变更，分析潜在问题
argument-hint: [diff-filter]
context: fork
agent: Explore
allowed-tools: Read, Grep, Bash(git:*)
model: sonnet
---

## Git 变更信息
- 当前分支：!`git branch --show-current`
- 变更差异：!`git diff $ARGUMENTS`
- 变更文件：!`git diff --name-status $ARGUMENTS`

## 审查任务
请基于上述信息分析：
1. 变更概述
2. 潜在问题（语法错误、安全漏洞、性能问题）
3. 改进建议
```
当这个技能运行时，执行顺序如下：

1. **命令执行** — 每个 `!` 命令立即执行（在 Claude 看到任何内容之前）
2. **内容替换** — 命令输出替换技能内容中的占位符
3. **提示渲染** — Claude 接收到包含实际数据的完全渲染的提示

### 子代理运行

当技能需要独立运行时（隔离上下文），在 frontmatter 中设置 `context: fork`。技能内容将作为驱动子代理的提示词，且无法访问你的对话历史。

**配置选项：**

| 字段 | 说明 | 可选值 |
|------|------|--------|
| `context: fork` | 启用子代理隔离模式 | 固定值 |
| `agent` | 指定子代理类型 | `Explore`、`Plan`、`general-purpose` 或自定义 |

> [子代理参考](#subagents-子代理)

### SKILL 模板

**完整模板（含字符串替换、动态上下文、子代理）：**

```markdown
---
name: skill-name
description: 简要描述 — [做什么] + [何时使用] + [触发关键词]
argument-hint: [参数提示，如: file]
context: fork
agent: Explore
allowed-tools: Read, Grep, Bash
model: sonnet
---

# [技能目的]

## 动态上下文
- [数据 1]：!`command $0`
- [数据 2]：!`command $ARGUMENTS`

## 工作流程

### 步骤 1：[操作名称]
[命令式指令]

### 步骤 2：[操作名称]
[命令式指令]

### 步骤 3：[操作名称]
[命令式指令]

## 输出格式
[结果应如何呈现]

### 异常处理
将异常处理保存到：log-${CLAUDE_SESSION_ID}.txt

## 资源
- scripts/：[可执行脚本说明]
- references/：[参考文档说明]
- assets/：[静态资源说明]
```
### 注意事项

- 使用命令式语言（"分析代码以..."）而不是第二人称（"您应该分析..."）
- 引用外部文件以获取详细内容，而不是嵌入所有内容
- 使用 {baseDir} 作为路径，永远不要硬编码绝对路径

## Slash Commands (自定义斜杠命令)

Slash Commands 允许你创建自定义的斜杠命令，用于快速调用特定功能或技能。

### 配置文件位置

斜杠命令配置文件位于：

```
C:\Users\<用户名>\.claude\commands\
```

在该目录下创建`markdown`文件来定义命令。

### 命令语法 
建议在命令文件顶部加 YAML frontmatter（命令与 Skill 共用同一套字段）
```yaml
---
description: 在命令列表中显示的说明
argument-hint: "[issue-number]"
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Bash(git:*), Bash(gh:*)
---
```

- description：用于命令列表展示，帮助团队知道“这个命令是干嘛的”
- argument-hint：自动补全时提示预期参数
- disable-model-invocation：设为 true 后，Claude 不会自动触发（只允许你手动执行 /command）
- allowed-tools：把命令的能力收敛到最小必要集合（尤其是包含写入/执行命令时）

#### 变量

| 变量 | 说明 | 适用场景 |
|------|------|---------|
| `$ARGUMENTS` | 获取命令的完整参数字符串（保留原始格式） | 传递给其他命令、需要保留空格/引号的场景 |
| `$1`、`$2`、`$3`... | 按空格拆分的单个参数 | 固定结构的命令（如 `/commit $1`） |

**示例对比：**

```bash
# 用户输入：/my-command "hello world" foo

# $ARGUMENTS = "hello world" foo（保留引号结构）
# $1 = "hello world"
# $2 = foo
```

## Subagents 子代理

Subagents（子代理）是专门处理特定类型任务的 AI 助手。  
每个 Subagent 在独立的上下文窗口中运行，拥有自定义系统提示、特定工具访问权限和独立权限设置

### 内置 Subagents

| 子代理 | 用途 |
|--------|------|
| `Explore` | 快速搜索和分析代码库（只读） |
| `Plan` | 收集上下文、设计实现方案 |
| `general-purpose` | 复杂多步骤任务 |
| `Bash` | 执行终端命令 |
| `claude-code-guide` | 回答 Claude Code 功能相关问题 |

> 💡 Subagents 通过 Skill 的 `agent` 参数配置使用，详见 [Skill](#skill) 章节

## Hooks 系统



## Headless 模式扇出
> 利用 Claude Code 的无头模式（Headless Mode），结合脚本工具（如 xargs、GNU parallel 或 Python 脚本），**并发地**启动多个 Claude Code 实例，同时对大量文件或任务进行处理  


**以下操作在git bash中:**
```bash
# 1. 查找文件
# 2. 排除不需要的目录
# 3. 使用 xargs 并行执行，并发数控制在 3
# 4. Prompt 明确、封闭
find note -name "*.md" | \
grep -v "/Java/" | \
xargs -P 3 -I {} \
claude -p "用一句话总结 {} 文件, 并标明文件名"

# 5. 后处理：格式化代码
black src/

# 6. 验证：运行检查
flake8 src/
```

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

## 配置/statusline
[Claude-code-statusline](./Claude-code-statusline.md)