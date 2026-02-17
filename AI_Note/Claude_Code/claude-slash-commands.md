# claude-slash-commands

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