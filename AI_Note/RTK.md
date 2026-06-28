# RTK (Rust Token Killer) 安装与配置教程

本文档介绍如何安装配置 RTK 工具，为 AI 编码助手（Claude Code 等）节省 60-90% 的 token 消耗。

- [RTK GitHub](https://github.com/rtk-ai/rtk)
- [官方文档](https://www.rtk-ai.app/docs/getting-started/installation/)
- [配置参考](https://www.rtk-ai.app/docs/getting-started/configuration/)

## 前提

- **Linux / macOS**（Windows 建议使用 WSL）
- **Claude Code**（或其他支持的 AI 编码工具）
- **~/.local/bin 在 PATH 中**

> ⚠️ **名称冲突**：`crates.io` 上存在同名包 `reachingforthejack/rtk`（Rust Type Kit），不要用 `cargo install rtk` 安装，必须指定 git 源。

## 安装

### Quick Install（推荐）

```bash
curl -fsSL https://raw.githubusercontent.com/rtk-ai/rtk/refs/heads/master/install.sh | sh
```

安装到 `~/.local/bin`。如果 PATH 中没有：

```bash
export PATH="$HOME/.local/bin:$PATH"
```

### 其他安装方式

```bash
# Homebrew
brew install rtk-ai/tap/rtk

# Cargo（指定 git 源避免名称冲突）
cargo install --git https://github.com/rtk-ai/rtk
```

### 验证安装

```bash
rtk --version   # 应显示 rtk x.y.z
rtk gain        # 应显示 token 节省统计
```

> 如果 `rtk gain` 失败但 `rtk --version` 成功，说明装错了包。执行 `cargo uninstall rtk` 后重新安装。

## 初始化 Claude Code Hook

```bash
rtk init -g
```

该命令会：

1. 注册 PreToolUse hook（自动将 Bash 命令重写为 `rtk` 等价命令）
2. 生成 `~/.claude/RTK.md` 指令文件
3. 在 `~/.claude/CLAUDE.md` 中添加 `@RTK.md` 引用
4. 提示修改 `~/.claude/settings.json`（可能需要手动操作）

如果自动 patch 未生效，手动在 `settings.json` 的 `hooks` 中添加：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "rtk hook claude"
          }
        ]
      }
    ]
  }
}
```

### 验证 Hook

```bash
rtk init --show
```

全部 `[ok]` 即为成功。完成后**重启 Claude Code**。

## 配置

配置文件路径：

| 平台 | 路径 |
|------|------|
| Linux | `~/.config/rtk/config.toml` |
| macOS | `~/Library/Application Support/rtk/config.toml` |

创建配置文件：

```bash
rtk config --create    # 生成默认配置
rtk config             # 查看当前配置
```

### 推荐配置

```toml
# RTK 配置文件

# ─── Token 使用追踪 ───
[tracking]
enabled = true       # 启用 token 节省统计
history_days = 90    # 历史数据保留天数

# ─── 显示设置 ───
[display]
colors = true        # 彩色输出
emoji = true         # 使用 emoji 图标
max_width = 120      # 最大输出宽度

# ─── 文件过滤 ───
# 作用于 ls, find, grep, cat 等文件类命令
# 匹配的路径会被排除，减少 AI 上下文噪声
[filters]
ignore_dirs = [
  ".git",
  "node_modules",
  "target",
  "__pycache__",
  ".venv",
  "vendor",
  ".next",
  "dist",
  "build",
  ".cache",
]
ignore_files = [
  "*.lock",
  "*.min.js",
  "*.min.css",
  "*.map",
  "*.snap",
]

# ─── 失败日志 ───
# 命令失败时保存完整原始输出到 ~/.local/share/rtk/tee/
# AI 可读取日志详情而无需重跑命令
[tee]
enabled = true      # 启用
mode = "failures"   # 仅失败时保存（可选: always, never）
max_files = 20      # 轮转保留最近 N 个文件
max_file_size = 1048576  # 单文件最大 1MB

# ─── 遥测 ───
[telemetry]
enabled = false     # 关闭匿名遥测

# ─── Hook 排除 ───
# 匹配的命令不会被自动重写
[hooks]
exclude_commands = [
  "^curl",          # 正则: 排除所有 curl
  "^wget",          # 正则: 排除所有 wget
  "git rebase",     # 交互式 git 命令
  "git cherry-pick",# 交互式 git 命令
  "docker exec",    # 交互式 docker 命令
]
```

### 环境变量

| 变量 | 说明 |
|------|------|
| `RTK_DISABLED=1` | 单次命令禁用 RTK（`RTK_DISABLED=1 git rebase`） |
| `RTK_TELEMETRY_DISABLED=1` | 全局禁用遥测 |
| `RTK_HOOK_AUDIT=1` | 启用 hook 审计日志 |
| `SKIP_ENV_VALIDATION=1` | 跳过环境变量验证（Next.js 项目有用） |

## 使用

Hook 安装后，所有 Bash 命令自动被重写：

```
git status      → rtk git status      # 紧凑状态
git diff        → rtk git diff        # 精简差异
cargo test      → rtk cargo test       # 失败摘要
ls              → rtk ls               # 优化目录树
```

### Meta 命令

```bash
rtk gain              # Token 节省统计
rtk gain --graph      # 30 天趋势图
rtk gain --history    # 命令历史
rtk discover          # 发现遗漏的优化机会
rtk proxy <cmd>       # 原始透传（调试用）
```

## 卸载

```bash
rtk init -g --uninstall     # 移除 hook、RTK.md、settings.json 条目
cargo uninstall rtk          # 移除二进制（Cargo 安装时）
brew uninstall rtk           # 移除二进制（Homebrew 安装时）
```
