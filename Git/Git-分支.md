# Git 分支

> Git 分支本质上是指向提交对象的可变指针。

## 一、分支基础

### 1.1 分支操作

| 操作 | 命令 | 说明 |
|:-----|:-----|:-----|
| 创建分支 | `git branch <name>` | 创建但不切换 |
| 创建并切换 | `git switch -c <name>` | 或 `git checkout -b <name>` |
| 切换分支 | `git switch <name>` | 或 `git checkout <name>` |
| 切换上一个分支 | `git switch -` | 返回上一个分支 |
| 删除本地分支 | `git branch -d <name>` | 需已合并 |
| 强制删除 | `git branch -D <name>` | 未合并也可删除 |
| 重命名分支 | `git branch -m <old> <new>` | 重命名当前分支 |
| 删除远程分支 | `git push origin --delete <name>` | 删除远程分支 |

### 1.2 查看分支

```bash
# 列出本地分支
git branch

# 列出所有分支（含远程）
git branch -a

# 查看每个分支最后一次提交
git branch -v

# 查看分支跟踪关系
git branch -vv

# 查看已合并/未合并分支
git branch --merged
git branch --no-merged
```

## 二、分支合并

### 2.1 合并方式

| 方式 | 命令 | 说明 |
|:-----|:-----|:-----|
| 普通合并 | `git merge <branch>` | 保留历史，有合并提交 |
| 快进合并 | 自动发生 | 分支线性前进，无合并提交 |
| 变基合并 | 先 `git rebase` 后合并 | 线性历史，无合并提交 |

### 2.2 合并冲突

```bash
# 合并后冲突时，手动编辑文件解决冲突
# 然后标记为已解决
git add <resolved-files>
# 完成合并
git commit
```

## 三、变基（Rebase）

> 变基用于将提交历史"重新应用"到另一个分支上，产生线性历史。

### 3.1 基本变基

```bash
# 将当前分支变基到 main
git rebase main
```

**效果：** 将当前分支在共同祖先之后的提交，重新应用到 main 的最新提交上。

### 3.2 变基指定分支

```bash
# 将 demo1 变基到 master
git rebase master demo1
```

### 3.3 变基 vs 合并

| 特性 | 变基 (Rebase) | 合并 (Merge) |
|:-----|:-------------|:-------------|
| 历史记录 | 线性，整洁 | 分支图，真实 |
| 提交历史 | 重写，新哈希 | 保留原哈希 |
| 使用场景 | 功能分支合并主分支 | 保留分支历史 |
| 注意事项 | **勿对已推送提交变基** | 无特殊限制 |

> **黄金法则：** 如果提交存在于你的仓库之外，而别人可能基于这些提交进行开发，则**不要执行变基**。

## 四、远程分支

### 4.1 跟踪分支

> 跟踪分支是与远程分支有直接关系的本地分支，`git pull` 会自动识别。

```bash
# 创建跟踪分支
git checkout -b <branch> <remote>/<branch>
# 或简写（本地无同名分支时）
git checkout <branch>

# 设置已有分支跟踪远程分支
git branch -u <remote>/<branch>
```

### 4.2 远程分支操作

```bash
# 查看远程分支
git branch -r

# 查看远程分支详细信息
git remote show <remote>

# 列出远程仓库的所有引用
git ls-remote <remote>
```

## 五、HEAD 指针与相对引用

### 5.1 分离头指针

> 当 HEAD 不指向任何分支时，称为"分离头指针"状态。此时提交不会影响任何分支。

```bash
# 检出指定提交（进入分离头指针）
git checkout <commit-id>

# 退出分离头指针
git switch -
```

### 5.2 相对引用

| 符号 | 说明 | 示例 |
|:-----|:-----|:-----|
| `^` | 上一个提交（父提交） | `HEAD^`、`master^` |
| `^2` | 第二个父提交（合并时） | `HEAD^2` |
| `~n` | 向上 n 个提交 | `HEAD~3` |
| `-f` | 强制分支指向指定提交 | `git branch -f master HEAD~3` |

```bash
# 切换到上一个提交
git checkout HEAD^

# 切换到向上 3 个提交
git checkout HEAD~3

# 强制分支指向某个提交
git branch -f <branch> <commit-id>
```
