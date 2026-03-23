# 按计划运行提示词

使用 `/loop` 和 cron 调度工具在 Claude Code 会话中重复运行提示词、轮询状态或设置一次性提醒。

> **会话范围**：任务仅在当前 Claude Code 进程中存在，退出后消失。持久调度请使用 [Desktop 计划任务](https://code.claude.com/docs/desktop) 或 [GitHub Actions](https://code.claude.com/docs/github-actions)。

---

## 使用 /loop 计划重复提示词

`/loop` 是计划重复提示词的最快方式。

```bash
/loop 5m check if the deployment finished and tell me what happened
```

### 间隔语法

| 形式 | 示例 | 解析的间隔 |
|------|------|-----------|
| 前导令牌 | `/loop 30m check the build` | 每 30 分钟 |
| 尾部 `every` 子句 | `/loop check the build every 2 hours` | 每 2 小时 |
| 无间隔 | `/loop check the build` | 默认每 10 分钟 |

**支持单位**：`s`（秒）、`m`（分钟）、`h`（小时）、`d`（天）

- 秒数向上舍入到最近分钟（cron 粒度为 1 分钟）
- 不均匀间隔（如 `7m`、`90m`）会舍入到整洁间隔

### 循环另一个命令

计划的提示词可以是命令或 skill 调用：

```bash
/loop 20m /review-pr 1234
```

---

## 设置一次性提醒

用自然语言描述，Claude 会创建单次触发任务（运行后自动删除）：

```bash
remind me at 3pm to push the release branch
```

```bash
in 45 minutes, check whether the integration tests passed
```

---

## 管理计划任务

| 命令 | 功能 |
|------|------|
| `what scheduled tasks do I have?` | 列出所有任务 |
| `cancel the deploy check job` | 取消任务 |

### 底层工具

| 工具 | 功能 |
|------|------|
| `CronCreate` | 创建任务（cron 表达式 + 提示词 + 重复标志） |
| `CronList` | 列出所有任务（ID、计划、提示词） |
| `CronDelete` | 按 ID 取消任务 |

- 每个任务有 8 字符 ID
- 会话最多同时保存 50 个任务

---

## 运行机制

### 调度逻辑

- 每秒检查到期任务，低优先级入队
- 在回合之间触发，不在 Claude 响应时触发
- 所有时间为**本地时区**（非 UTC）

### 抖动（避免 API 峰值）

| 任务类型 | 抖动规则 |
|---------|---------|
| 重复任务 | 最多晚触发周期的 10%，上限 15 分钟 |
| 一次性任务（:00/:30） | 最多提前 90 秒触发 |

> 精确时间需求：选择非 `:00` 或 `:30` 的分钟（如 `3 9 * * *` 而非 `0 9 * * *`）

### 三天过期

重复任务创建后 **3 天自动过期**。需更长时间请在过期前重新创建。

---

## Cron 表达式参考

标准 5 字段格式：`minute hour day-of-month month day-of-week`

| 示例 | 含义 |
|------|------|
| `*/5 * * * *` | 每 5 分钟 |
| `0 * * * *` | 每小时整点 |
| `7 * * * *` | 每小时第 7 分钟 |
| `0 9 * * *` | 每天本地时间 9am |
| `0 9 * * 1-5` | 工作日 9am |
| `30 14 15 3 *` | 3 月 15 日 2:30pm |

**语法支持**：通配符 `*`、单个值 `5`、步长 `*/15`、范围 `1-5`、列表 `1,15,30`

**星期**：`0`/`7` = 星期日，`6` = 星期六

**不支持**：`L`、`W`、`?`、名称别名（`MON`、`JAN`）

---

## 禁用计划任务

```bash
export CLAUDE_CODE_DISABLE_CRON=1
```

禁用后 cron 工具和 `/loop` 不可用。

---

## 限制

| 限制 | 说明 |
|------|------|
| 会话依赖 | 任务仅在 Claude Code 运行且空闲时触发 |
| 无追赶 | 错过触发只执行一次，不补齐 |
| 无持久化 | 重启 Claude Code 清除所有任务 |

**持久化替代方案**：
- [GitHub Actions](https://code.claude.com/docs/github-actions) `schedule` 触发器
- [Desktop 计划任务](https://code.claude.com/docs/desktop) 图形化设置
