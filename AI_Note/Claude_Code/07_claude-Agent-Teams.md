# Claude Code Agent Teams 

> **来源**：[协调 Claude Code 会话团队](https://code.claude.com/docs/zh-CN/agent-teams)

## 概念

**Agent Teams** = 多个独立 Claude 实例协同工作，每个有独立 context，可直接通信

---

## 启用

```json
// settings.json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

**或** 环境变量：`export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`

---

## 与 Subagents 对比

| 特性 | Subagents | Agent Teams |
|:-----|:----------|:------------|
| Context | 独立，结果返回主代理 | 完全独立 |
| 通信 | 仅向主代理报告 | 队友直接通信 |
| 协调 | 主代理管理 | 共享任务列表，自我协调 |
| 适用 | 专注任务，只看结果 | 需要讨论协作的复杂工作 |
| 成本 | 较低 | 较高（每队友独立实例） |

---

## 快速开始

```bash
# 启动团队
claude

# 自然语言描述
Create an agent team to review this PR. Spawn three reviewers:
- One focused on security
- One checking performance
- One validating test coverage
```

---

## 控制团队

| 操作 | 命令 |
|:-----|:-----|
| **显示模式** | `teammateMode: "in-process"` / `"split-panes"` |
| **指定队友数量** | `Create a team with 4 teammates` |
| **计划批准** | `Require plan approval before changes` |
| **直接对话** | `Shift+Down` 切换队友（in-process） |
| **关闭队友** | `Ask the researcher to shut down` |
| **清理团队** | `Clean up the team` |

---

## 显示模式

| 模式 | 说明 | 要求 |
|:-----|:-----|:-----|
| **In-process** | 队友在主终端内运行，`Shift+Down` 切换 | 无 |
| **Split-panes** | 每个队友独立窗格 | tmux 或 iTerm2 |

---

## 最佳实践

| 实践 | 建议 |
|:-----|:-----|
| **团队规模** | 3-5 个队友（平衡并行与协调） |
| **任务大小** | 自包含单位（函数/测试/审查） |
| **避免冲突** | 不同队友分配不同文件集 |
| **监控指导** | 定期检查进度，综合发现 |
| **新手入门** | 从 PR 审查/研究/调查开始 |

---

## 权限

- 队友**继承负责人权限**
- 权限请求冒泡到负责人
- 预批准常用操作减少中断

---

## 架构

Agent team 由以下部分组成：

| 组件 | 角色 |
|:-----|:-----|
| **Team lead** | 创建团队、生成队友并协调工作的主 Claude Code 会话 |
| **Teammates** | 各自处理分配任务的独立 Claude Code 实例 |
| **Task list** | 队友认领和完成的共享工作项列表 |
| **Mailbox** | 代理之间通信的消息系统 |

团队和任务存储在本地：
```
~/.claude/
├── teams/{team-name}/config.json  # 团队配置（成员列表）
└── tasks/{team-name}/             # 共享任务列表
```
> 团队配置包含一个 members 数组，其中包含每个队友的名称、代理 ID 和代理类型。队友可以读取此文件以发现其他团队成员。

---

## 限制（实验性）

| 限制 | 说明 |
|:-----|:-----|
| **会话恢复** | In-process 队友无法恢复 |
| **任务状态** | 可能滞后，需手动更新 |
| **关闭慢** | 队友完成当前请求后才关闭 |
| **每会话一队** | 启动新团队前需清理 |
| **无嵌套团队** | 队友无法生成团队 |

---
