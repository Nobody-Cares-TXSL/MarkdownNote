# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

这是一个 Markdown 笔记仓库，用于记录各种技术笔记和学习文档。

## 仓库结构

笔记按主题分类组织：

| 目录 | 内容 |
|------|------|
| `Git/` | Git 相关操作和配置 |
| `Ubuntu/` | Linux 系统管理和命令 |
| `Java/` | Java 和 Spring Boot 开发 |
| `Web/` | 前端技术（Vue、CSS、ES6） |
| `Python/` | Python 相关笔记 |
| `MySQL/` | 数据库和 MyBatis |
| `Redis/` | Redis 缓存 |
| `AI_Note/` | AI 工具使用笔记（Claude Code、Gemini 等） |
| `Data_Structure_Note/` | 数据结构（树、哈希等） |
| `系统设计/` | 系统设计相关（RESTful API） |
| `Markdown/` | Markdown 语法 |
| `Lua/` | Lua 脚本语言 |
| `PowerShell/` | PowerShell 脚本 |
| `IMAGES/` | 图片资源 |

## Git 提交规范

使用**约定式提交（Conventional Commits）**格式：

```
<type>(<scope>): <description>
```

**Type 类型**：
- `docs` - 文档更新
- `fix` - 修复 Bug
- `feat` - 新功能
- `refactor` - 重构
- `style` - 代码格式调整
- `chore` - 构建或辅助工具变动

**示例**：
```
docs(Git): 新增 SSH 代理配置
docs(Ubuntu): 修正 journalctl 参数说明
```

**提交时必须使用 HEREDOC 格式**：
```bash
git commit -m "$(cat <<'EOF'
<type>(<scope>): <description>

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
EOF
)"
```

## 远程仓库

项目配置了多个远程仓库：

- **origin** - Gitee 主仓库 + GitHub 镜像
- **github** - GitHub 独立远程

推送时需要同时更新：
```bash
git push origin   # 推送到 Gitee 和 GitHub
git push github   # 推送到 GitHub 独立远程
```

## 文档风格

- 使用中文编写
- 技术术语保留原文
- 代码示例必须可运行
- 保持简洁，避免冗余
- 参数说明使用表格格式
