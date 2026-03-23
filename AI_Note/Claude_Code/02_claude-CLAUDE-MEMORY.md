# CLAUDE.md 文件

> 经验法则： 保持 `CLAUDE.md` 在 200 行以下。如果它在增长，用 `@path/to/import` 导入语法将参考内容导入其他文件 或拆分为 `.claude/rules/` 文件。
>
> 导入的文件在启动时展开并加载到上下文中，与引用它们的 `CLAUDE.md` 一起
>
> `/memory` 命令列出在当前会话中加载的所有 `CLAUDE.md` 和规则文件，让您切换自动记忆开或关，并提供打开自动记忆文件夹的链接。


## CLAUDE.md 文件的位置

| 范围 | 位置 | 目的 | 用例示例 | 共享对象 |
|------|------|------|----------|----------|
| **托管策略** | • macOS: `/Library/Application Support/ClaudeCode/CLAUDE.md`<br>• Linux 和 WSL: `/etc/claude-code/CLAUDE.md`<br>• Windows: `C:\Program Files\ClaudeCode\CLAUDE.md` | 由 IT/DevOps 管理的组织范围指令 | 公司编码标准、安全策略、合规要求 | 组织中的所有用户 |
| **项目指令** | `./CLAUDE.md` 或 `./.claude/CLAUDE.md` | 项目的团队共享指令 | 项目架构、编码标准、常见工作流 | 通过源代码控制的团队成员 |
| **用户指令** | `~/.claude/CLAUDE.md` | 所有项目的个人偏好 | 代码样式偏好、个人工具快捷方式 | 仅你（所有项目） |
| **本地指令** | `./CLAUDE.local.md` | 个人项目特定偏好，未检入 git | 你的沙箱 URL、首选测试数据 | 仅你（当前项目） |

## CLAUDE.local.md 文件

**用途**：存放个人的、不想提交到 git 的项目配置。

**特点**：
- 会自动加载（和 `CLAUDE.md` 一起生效）
- 自动加入 `.gitignore`，不会误提交

## .claude/rules/ 组织规则

**用途**：存放项目的组织规则，如编码标准、常见工作流等。

> 规则在每个会话或打开匹配文件时加载到上下文中。

### 设置规则
> 在项目的 `.claude/rules/` 目录中放置 markdown 文件。
>
> 所有 `.md` 文件都被递归发现，因此你可以将规则组织到子目录中，如 `frontend/` 或 `backend/`

### 路径范围规则
规则可以使用带有 paths 字段的 YAML frontmatter 范围限定到特定文件。
> 例如，`paths: ["src/**/*.js"]` 会将规则应用于 `src/` 目录下的所有 JavaScript 文件。

- 没有 paths 字段的规则无条件加载并适用于所有文件。
- 路径范围规则在 Claude 读取与模式匹配的文件时触发，而不是在每次工具使用时。
- 在 paths 字段中使用 glob 模式按扩展名、目录或任何组合匹配文件
- 可以指定多个模式并使用大括号扩展在一个模式中匹配多个扩展名
> 例如，`paths: ["src/**/*.{js,jsx}"]` 会将规则应用于 `src/` 目录下的所有 JavaScript 和 JSX 文件。

举例：
```yaml
---
paths:
  - "src/**/*.{ts,tsx}" // 匹配 src 目录下的所有 TypeScript 和 TypeScript React 文件
  - "lib/**/*.ts" // 匹配 lib 目录下的所有 TypeScript 文件
  - "tests/**/*.test.ts" // 匹配 tests 目录下的所有测试文件
---
```
### 符号链接跨项目共享规则

**用途**：在多个项目间共享同一套规则，避免重复复制。

**用法**：

```bash
# 链接整个共享目录
# 访问 .claude/rules/shared 目录时，显示 ~/shared-claude-rules 目录中的文件
ln -s ~/shared-claude-rules .claude/rules/shared

# 链接单个文件
# 访问 .claude/rules/security.md 时，显示 ~/company-standards/security.md 文件
ln -s ~/company-standards/security.md .claude/rules/security.md
```

**效果**：
- 修改共享目录中的文件，所有链接的项目自动生效
- 自动检测循环链接，不会出错

### 排除特定的 CLAUDE.md 文件

**用途**：在大型项目中跳过不需要的 CLAUDE.md 文件或规则。

**配置**：在 `.claude/settings.local.json` 中添加：

例如：
```json
{
  "claudeMdExcludes": [
    "**/monorepo/CLAUDE.md", // 忽略 monorepo 目录下的 CLAUDE.md 文件
    "/home/user/monorepo/other-team/.claude/rules/**" // 排除其他团队的所有规则文件
  ]
}
```

**效果**：指定的文件会被忽略，不会被加载到上下文中。

## 自动记忆
自动记忆让 Claude 在你不编写任何内容的情况下跨会话积累知识。

Claude 不会每个会话都保存内容。它根据信息在未来对话中是否有用来决定值得记住什么。

### 启用或禁用自动记忆
- 自动记忆默认开启。要切换它，在会话中打开 /memory 并使用自动记忆切换，或在项目设置中设置 `autoMemoryEnabled` 为 false。
- 要通过环境变量禁用自动记忆，设置 CLAUDE_CODE_DISABLE_AUTO_MEMORY=1。

### 存储位置

**位置**：`~/.claude/projects/<project>/memory/`

同一仓库的所有 worktrees 和子目录共享一个记忆目录。

目录包含 `MEMORY.md` 入口点和可选的主题文件：

```
~/.claude/projects/<project>/memory/
├── MEMORY.md          # 简洁索引，加载到每个会话
├── debugging.md       # 调试模式的详细笔记
├── api-conventions.md # API 设计决策
└── ...                # Claude 创建的任何其他主题文件
```

**MEMORY.md**：
- 前 200 行 → 每次会话自动加载
- 超过 200 行 → 会话开始时不加载
- 详细内容放单独主题文件（按需读取）

> 此 200 行限制仅适用于`MEMORY.md`。`CLAUDE.md` 文件无论长度如何都完整加载，尽管较短的文件会产生更好的遵守度。
> 
> 保持 `MEMORY.md` 简洁，详细内容用 `@path` 引用或放单独文件。