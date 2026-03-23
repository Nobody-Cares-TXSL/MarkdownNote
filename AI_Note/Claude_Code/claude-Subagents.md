# Claude Code Subagents

## 概念

> 子代理是专门处理特定任务的 AI 助手，在**独立上下文**中运行
>
> `/agents` 命令提供了一个交互式界面来管理 subagents。
>
> Subagent 文件使用 `YAML frontmatter` 进行配置，后跟 Markdown 中的系统提示。

**特点**：
- 继承父对话的权限
- 有独立的工具限制
- 适当时自动调用

---

## 内置子代理

### 主要子代理

| 子代理 | 用途 | 工具权限 |
|:-------|:-----|:---------|
| `Explore` | 快速搜索分析代码库 | 只读工具（Glob, Grep, Read） |
| `Plan` | 收集上下文、设计方案 | 所有工具（除 Edit, Write, NotebookEdit） |
| `general-purpose` | 复杂多步骤任务 | 全部工具 |

### 辅助代理（自动调用）

| 代理 | 模型 | 触发时机 |
|:-----|:-----|:---------|
| `Bash` | 继承 | 需要独立上下文执行终端命令 |
| `statusline-setup` | Sonnet | 运行 `/statusline` 配置状态行 |
| `claude-code-guide` | Haiku | 询问 Claude Code 功能问题时 |

---

## 支持的 frontmatter 字段

> 以下字段可以在 YAML frontmatter 中使用。仅 `name` 和 `description` 是必需的。

| 字段 | 必需 | 描述 |
|:-----|:----:|:-----|
| `name` | ✅ | 使用小写字母和连字符的唯一标识符 |
| `description` | ✅ | Claude 何时应委托给此 subagent |
| `tools` | ❌ | 可使用的工具列表，省略则继承所有工具 |
| `disallowedTools` | ❌ | 禁用的工具，从继承/指定列表中删除 |
| `model` | ❌ | 模型：`sonnet`/`opus`/`haiku`/`inherit`，默认 `inherit(与主对话相同的模型)` |
| `permissionMode` | ❌ | 权限模式：`default`/`acceptEdits`/`dontAsk`/`bypassPermissions`/`plan` |
| `maxTurns` | ❌ | subagent 停止前的最大轮数 |
| `skills` | ❌ | 启动时加载的 Skills（注入完整内容，不继承父对话技能） |
| `mcpServers` | ❌ | 可用的 MCP servers（引用名称或内联定义） |
| `hooks` | ❌ | 限定于此 subagent 的生命周期 hooks （参考：[Hooks 文档](./claude-Hooks系统.md#skills-和-subagents-中的-hooks)）|
| `memory` | ❌ | 持久内存范围：`user`/`project`/`local`，启用跨会话学习 |
| `background` | ❌ | 设为 `true` 始终作为后台任务运行，默认 `false` |
| `isolation` | ❌ | 设为 `worktree` 在临时 git worktree 中运行（无更改自动清理） |

---

## Subagent 范围

| 位置 | 范围 | 优先级 | 创建方式 |
|:-----|:-----|:-------|:---------|
| `--agents` CLI 标志 | 当前会话 | 1（最高） | 启动时传递 JSON |
| `.claude/agents/` | 当前项目 | 2 | 交互式或手动 |
| `~/.claude/agents/` | 所有项目 | 3 | 交互式或手动 |
| 插件的 `agents/` 目录 | 启用插件的位置 | 4（最低） | 随插件安装 |

---

## CLI 定义 Subagent

> **用途**：快速测试或自动化脚本（仅当前会话有效，不保存到磁盘）

### 示例

```bash
claude --agents '{
  "code-reviewer": {
    "description": "Expert code reviewer. Use proactively after code changes.",
    "prompt": "You are a senior code reviewer. Focus on code quality, security, and best practices.",
    "tools": ["Read", "Grep", "Glob", "Bash"],
    "model": "sonnet"
  },
  "debugger": {
    "description": "Debugging specialist for errors and test failures.",
    "prompt": "You are an expert debugger. Analyze errors, identify root causes, and provide fixes."
  }
}'
```

### 支持的字段

`--agents` 标志接受与基于文件的 subagents 相同的 frontmatter 字段的 JSON。

| 字段 | 必需 | 描述 |
|------|:----:|------|
| `description` | 是 | 何时应调用 subagent 的自然语言描述 |
| `prompt` | 是 | 指导 subagent 行为的系统提示 |
| `tools` | 否 | 可用工具数组，如 `["Read", "Edit", "Bash"]`。省略则继承全部 |
| `disallowedTools` | 否 | 明确拒绝的工具名称数组 |
| `model` | 否 | 模型别名：`sonnet`/`opus`/`haiku`/`inherit`（默认） |
| `skills` | 否 | 预加载到上下文的 skill 名称数组 |
| `mcpServers` | 否 | MCP servers 数组（服务器名或 `{name: config}` 对象） |
| `maxTurns` | 否 | subagent 停止前的最大代理转数 |

---

## 限制 Subagent 生成 (Agent(agent_type) 语法)

> **适用范围**：仅限 `claude --agent` 启动的**主线程代理**

### 概念

代理可以生成其他 subagent 来并行处理任务。通过 `Agent()` 语法限制可生成的类型。

### 语法

```yaml
---
name: coordinator
description: Coordinates work across specialized agents
tools: Agent(worker, researcher), Read, Bash
---
```

**含义**：coordinator 只能生成 `worker` 和 `researcher` 类型的 subagent

### 类型来源

- **文件**：`.claude/agents/worker.md` → 类型 `worker`
- **CLI**：`--agents '{"worker": {...}}'` → 类型 `worker`

### 配置规则

| 写法 | 效果 |
|:-----|:-----|
| `Agent(type1, type2)` | 只允许生成指定类型（允许列表） |
| `Agent` | 允许生成任何类型 |
| 省略 `Agent` | 禁止生成任何 subagent |

### 注意

- Subagents **无法生成其他 subagents**
- `Task(...)` 仍作为 `Agent(...)` 别名工作

---

## 权限模式

> **permissionMode 字段作用**：控制 subagent 如何处理权限提示（继承父对话权限上下文，可覆盖）

### 模式对照

| 模式 | 行为 |
|:-----|:-----|
| `default` | 标准权限检查，需要用户确认 |
| `acceptEdits` | 自动接受文件编辑 |
| `dontAsk` | 自动拒绝权限提示（显式允许的工具仍有效） |
| `bypassPermissions` | 跳过所有权限检查 |
| `plan` | Plan mode（只读探索） |

### 示例

```yaml
---
name: auto-fixer
permissionMode: acceptEdits  # 自动接受编辑
tools: Edit, Read, Bash
---
```

### 注意

- ⚠️ **谨慎使用 `bypassPermissions`**：允许 subagent 执行任何操作无需批准
- 🔒 父级使用 `bypassPermissions` 时优先，无法被覆盖

---

## Skills 预加载

> **skills 字段作用**：启动时将 skill 完整内容注入 subagent 上下文，提供领域知识

### 语法

```yaml
---
name: api-developer
description: Implement API endpoints following team conventions
skills:
  - api-conventions
  - error-handling-patterns
tools: Edit, Read, Bash
---
```

### 核心特点

| 特点 | 说明 |
|:-----|:-----|
| **完整注入** | skill 完整内容注入上下文，非调用时加载 |
| **不继承** | subagents 不继承父对话的 skills，需明确列出 |
| **预加载** | 启动时加载，无需执行期间发现 |

### 与 `context: fork` 对比

| 方式 | 控制权 | 说明 |
|:-----|:-------|:-----|
| `skills` 字段 | subagent 控制 | subagent 加载 skill 内容 |
| `context: fork` | skill 控制 | skill 内容注入到指定代理 |

> 两者使用相同的底层系统

---

## memory持久内存

> **memory 字段作用**：让 subagent 跨对话积累知识（代码模式、调试见解、架构决策）

### 语法

```yaml
---
name: code-reviewer
description: Reviews code for quality and best practices
memory: user
---
```

### 内存范围

| 范围 | 位置 | 用途 |
|:-----|:-----|:-----|
| `user` | `~/.claude/agent-memory/<name>/` | 所有项目共享（推荐默认） |
| `project` | `.claude/agent-memory/<name>/` | 项目特定，可版本控制 |
| `local` | `.claude/agent-memory-local/<name>/` | 项目特定，不提交版本控制 |

### 启用后效果

- 系统提示包含读写内存目录的说明
- 自动加载 `MEMORY.md` 前 200 行
- 自动启用 `Read`、`Write`、`Edit` 工具

### 最佳实践

```yaml
# 在 subagent 文件的 markdown 正文（系统提示）中添加：
---
# 1. 工作中持续更新
Update your memory as you discover patterns, conventions, and key decisions.

# 2. 任务开始前查阅
Check your memory before starting work: "Review your memory for patterns."

# 3. 任务完成后保存
Save to memory after completing: "Save what you learned."
---
```

---

## Hooks

> **hooks 字段作用**：在 subagent 生命周期特定事件时执行脚本，实现动态工具验证和自动化工作流

### 条件规则（PreToolUse）

**用途**：工具执行前验证操作，允许某些操作同时阻止其他操作

#### 只读数据库示例

```yaml
---
name: db-reader
description: Execute read-only database queries
tools: Bash
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/validate-readonly-query.sh"
---
```

#### 验证脚本示例

```bash
#!/bin/bash
# ./scripts/validate-readonly-query.sh
INPUT=$(cat)
COMMAND=$(echo "$INPUT" | jq -r '.tool_input.command // empty')

# 阻止 SQL 写操作
if echo "$COMMAND" | grep -iE '\b(INSERT|UPDATE|DELETE|DROP|CREATE|ALTER|TRUNCATE)\b' > /dev/null; then
  echo "Blocked: Only SELECT queries are allowed" >&2
  exit 2
fi
exit 0
```

**Hook 输入**：通过 stdin 传递 JSON，包含 `tool_input.command` 等字段

**退出代码**：`0`=允许，`2`=阻止操作

---

### 禁用特定 Subagents

#### settings.json 方式

> 使用 `Agent(subagent-name)` 格式禁止特定 subagent（匹配 name 字段）
>
>  settings 中的 permissions.deny 数组来防止 Claude 使用特定 subagents

```json
{
  "permissions": {
    "deny": ["Agent(Explore)", "Agent(my-custom-agent)"]
  }
}
```

#### CLI 方式

```bash
claude --disallowedTools "Agent(Explore)"
```

---

### Subagent Frontmatter Hooks

**适用事件**：

| 事件 | 匹配器 | 触发时机 |
|:-----|:-------|:---------|
| `PreToolUse` | 工具名 | 工具使用前 |
| `PostToolUse` | 工具名 | 工具使用后 |
| `Stop` | （无） | Subagent 完成时 |

#### 验证命令 + Linter 示例

```yaml
---
name: code-reviewer
description: Review code changes with automatic linting
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/validate-command.sh $TOOL_INPUT"
  PostToolUse:
    - matcher: "Edit|Write"
      hooks:
        - type: command
          command: "./scripts/run-linter.sh"
---
```
> Frontmatter 中的 Stop hooks 会自动转换为 SubagentStop 事件。

---

### 项目级 Hooks（settings.json）

**适用事件**：

| 事件 | 匹配器 | 触发时机 |
|:-----|:-------|:---------|
| `SubagentStart` | 代理类型名 | Subagent 开始执行 |
| `SubagentStop` | 代理类型名 | Subagent 完成时 |

#### 数据库连接示例

```json
{
  "hooks": {
    "SubagentStart": [
      {
        "matcher": "db-agent",
        "hooks": [
          { "type": "command", "command": "./scripts/setup-db-connection.sh" }
        ]
      }
    ],
    "SubagentStop": [
      {
        "hooks": [
          { "type": "command", "command": "./scripts/cleanup-db-connection.sh" }
        ]
      }
    ]
  }
}
```

---

### Hooks 配置对比

| 配置位置 | 作用域 | 适用事件 |
|:---------|:-------|:---------|
| **Frontmatter** | 仅该 subagent | PreToolUse, PostToolUse, Stop |
| **settings.json** | 主会话响应 | SubagentStart, SubagentStop |

---

## 使用方式

```bash
# 通过 Skill 的 agent 参数配置
# 或由 Claude 自动调用
```

> 📖 详见 [Skill](./claude-skills.md) 章节
