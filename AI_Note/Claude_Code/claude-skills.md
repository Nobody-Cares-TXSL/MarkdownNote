# claude-skills

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

一个 Skill 是一个目录，入口文件固定为 `SKILL.md`：  
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
> 把大段参考资料拆到单独文件，并在 `SKILL.md` 里链接它们，能让上下文更“轻”，也更容易维护。

### SKILL的渐进式披露

- 元数据（~100 个符号）： name 和 description 字段在所有技能启动时加载
- 说明（建议<5000 个符号）：技能激活时加载完整的 `SKILL.md` 正文
- 资源（按需使用）：文件（例如 scripts/ 、 references/ 或 assets/ 中的文件）仅在需要时加载

> 保持你的主 `SKILL.md` 在 500 行以下。将详细的参考材料移动到单独的文件中。

### `SKILL.md` 文件
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

> [子代理参考](./claude-Subagents.md)

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