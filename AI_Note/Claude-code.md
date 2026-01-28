# Claude Code

> Anthropic 官方的 Claude AI 命令行工具，支持代码编写、调试、文件操作等功能。

## 安装

使用 Windows 包管理器 winget 安装：

```powershell
winget install Anthropic.ClaudeCode
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

### 基本命令

| 命令 | 说明 |
|------|------|
| `claude` | 启动 Claude Code |
| `claude update` | 更新到最新版本 |
| `/help` | 查看帮助信息 |
| `/clear` | 清空对话历史 |
| `/exit` | 退出程序 |
| `/plugin marketplace list` | 列出所有已添加的市场 |
| `/terminal-setup` | 启用便捷的终端集成功能，例如支持按 Shift + Enter 换行 |

## 添加市场并安装插件

**添加市场：**

```powershell
# 直接用 HTTPS 添加市场
/plugin marketplace add HTTPS

# 例如增加 anthropics 的 skills 仓库
/plugin marketplace add https://github.com/anthropics/skills
```

> 本质上就是帮你自动执行了一次 git clone，把远程仓库拉取到了你本地的 `C:\Users\<用户名>\.claude\plugins\marketplaces` 目录下

**安装插件：**

```powershell
# 安装 anthropics/skills 仓库中的插件包
/plugin install document-skills@anthropic-agent-skills
/plugin install example-skills@anthropic-agent-skills
```

> 安装到 `C:\Users\<用户名>\.claude\plugins\cache` 目录下    

重启 Claude Code 后即可生效

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

### Skill 目录结构与支持文件

一个 Skill 是一个目录，入口文件固定为 SKILL.md：
```
my-skill/
├── SKILL.md           # 必需：入口与主要指令
├── reference.md       # 可选：大段参考资料（需要时再加载）
├── examples/          # 可选：示例输出
└── scripts/           # 可选：可执行脚本（执行，不自动注入上下文）
```
> 把大段参考资料拆到单独文件，并在 SKILL.md 里链接它们，能让上下文更“轻”，也更容易维护。

### Frontmatter 参考（常用字段）

SKILL.md 顶部的 frontmatter 用来控制调用行为（字段均可选，但建议写 description）：

```yaml
---
name: my-skill
description: 这个 Skill 做什么，何时使用
disable-model-invocation: true
allowed-tools: Read, Grep
---
```

| 字段 | 说明 |
|------|------|
| `name` | Skill 名称（省略则使用目录名）；只允许小写字母/数字/连字符（最长 64） |
| `description` | 推荐填写；Claude 会用它判断何时自动加载 |
| `argument-hint` | 自动补全时显示的参数提示（例如 `[issue-number]`、`[file] [format]`） |
| `disable-model-invocation` | 设为 true 后，Claude 不会自动触发，只能由你通过 `/name` 手动触发 |
| `user-invocable` | 设为 false 后，从 `/` 菜单隐藏（更适合"背景知识"类 Skill） |
| `allowed-tools` | Skill 激活时允许"无需再次确认"可用的工具范围 |
| `model` | Skill 激活时使用的模型 |
| `context` | 设为 fork 时在隔离的子代理上下文运行 |
| `agent` | `context: fork` 时选择子代理类型（例如 Explore、Plan） |
| `hooks` | 仅对该 Skill 生效的 hooks（格式见 Hooks 文档） |

### 字符串替换（参数与会话 ID）

| 变量 | 含义 | 示例 | 
|------|------|------|
| `$ARGUMENTS` | 调用 Skill 时传入的全部参数（若内容里未出现，Claude Code 会在末尾追加 `ARGUMENTS: ...`） | /review src/ → $ARGUMENTS 变成 src/ |
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