# CLAUDE.md

技术笔记仓库，按主题分目录组织。详细规范见 @./Git/Git提交规范.md。

## Git 提交规范

@./Git/Git提交规范.md

## 远程仓库

项目配置双远程冗余备份，推送时**两个都必须执行**：

```bash
git push origin   # Gitee 主仓库 + GitHub 镜像
git push github   # GitHub 独立远程
```

> **Why 双推送**：origin 的 pushurl 同时指向 Gitee 和 GitHub，但因 TLS 不稳定经常失败，所以额外配置了 github 远程作为备用通道。

## 文档风格

- 中文编写，技术术语保留原文
- 代码示例必须可运行
- 参数说明用表格，内容保持简洁
