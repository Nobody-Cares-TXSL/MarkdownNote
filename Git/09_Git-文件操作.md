# Git 文件操作

> Git 中对文件的移动、重命名、删除、清理与恢复操作。

## 一、移动与重命名（git mv）

### 1.1 语法

```bash
# 重命名文件
$ git mv <old_name> <new_name>

# 移动文件到指定目录
$ git mv <file> <directory>/

# 批量移动（使用通配符）
$ git mv *.js src/
```

### 1.2 为什么用 `git mv` 而非普通 `mv`

> 普通 `mv` 还需手动 `git rm` 旧路径 + `git add` 新路径，容易遗漏

| 操作 | Git 识别为 | 历史追溯 |
|:-----|:----------|:---------|
| `git mv` | 重命名 | `git log --follow <new_name>` 可追踪重命名前的提交 |
| `mv` + `git rm` + `git add` | 重命名（效果相同） | 同上 |
| `mv` 后忘记 `git add` | 删除旧文件 + 未跟踪新文件 | 历史断裂 |

运行 `git mv` 等价于以下三条命令：

```bash
$ mv README.md README
$ git rm README.md
$ git add README
```

### 1.3 查看重命名历史

```bash
# 追踪文件重命名前的提交记录
$ git log --follow <file>

# 查看重命名时的差异（含旧文件名内容）
$ git log -p --follow <file>

# 列出所有重命名操作
$ git log --diff-filter=R --summary

# 查看重命名相似度（默认 50%）
$ git diff -M
```

### 1.4 目录重命名

```bash
# 重命名目录
$ git mv <old_dir> <new_dir>

# 大小写重命名（需要两步，因为文件系统不区分大小写）
$ git mv MyDir temp_dir
$ git mv temp_dir mydir
```

## 二、删除文件（git rm）

### 2.1 基本用法

```bash
# 从工作区和暂存区同时删除
$ git rm <file>

# 从暂存区删除，但保留工作区文件
$ git rm --cached <file>

# 强制删除（文件有未提交的修改时）
$ git rm -f <file>

# 使用 glob 模式批量删除
$ git rm *.log
$ git rm -r dir/
```

### 2.2 删除场景

| 场景 | 命令 |
|:-----|:-----|
| 删除已跟踪的文件 | `git rm <file>` |
| 停止跟踪但保留文件 | `git rm --cached <file>` |
| 删除有本地修改的文件 | `git rm -f <file>` |
| 只删除暂存区的修改 | `git restore --staged <file>` |

> `git rm --cached` 常用于将已提交的文件加入 `.gitignore` 后取消跟踪

## 三、恢复文件（git restore）

> Git 2.23+ 引入 `git restore`，替代了部分 `git checkout` 的功能

### 3.1 基本用法

```bash
# 恢复工作区文件（丢弃未暂存的修改）
$ git restore <file>

# 恢复暂存区文件（取消 git add）
$ git restore --staged <file>

# 同时恢复工作区和暂存区
$ git restore --staged --worktree <file>

# 从指定提交恢复文件
$ git restore --source=<commit> <file>

# 从另一个分支恢复文件
$ git restore --source=<branch> <file>
```

### 3.2 恢复场景

| 场景 | 命令 |
|:-----|:-----|
| 丢弃工作区的修改 | `git restore <file>` |
| 取消 `git add` | `git restore --staged <file>` |
| 恢复误删的文件 | `git restore <file>` |
| 回退到某次提交的版本 | `git restore --source=<commit> <file>` |
| 从其他分支借用文件 | `git restore --source=<branch> <file>` |

## 四、清理未跟踪文件（git clean）

### 4.1 基本用法

```bash
# 查看将被删除的文件（不实际删除）
$ git clean -n

# 删除未跟踪的文件
$ git clean -f

# 删除未跟踪的文件和目录
$ git clean -fd

# 同时删除被 .gitignore 忽略的文件
$ git clean -fdx

# 只删除被 .gitignore 忽略的文件
$ git clean -fX
```

### 4.2 选项说明

| 选项 | 说明 |
|:-----|:-----|
| `-n` | 预览模式，只显示将删除的文件 |
| `-f` | 强制删除文件 |
| `-d` | 包含目录 |
| `-x` | 包含被 .gitignore 忽略的文件 |
| `-X` | 只删除被 .gitignore 忽略的文件 |
| `-i` | 交互式选择删除 |

> `git clean -fd` 和 `git restore .` 组合使用可以完全重置工作区

## 五、暂存文件（git stash）

> 临时保存工作区和暂存区的修改，恢复到干净状态

### 5.1 基本用法

```bash
# 暂存当前修改
$ git stash

# 暂存时添加备注
$ git stash push -m "备注信息"

# 暂存包括未跟踪的文件
$ git stash -u

# 查看暂存列表
$ git stash list

# 恢复最近一次暂存（不删除）
$ git stash apply

# 恢复指定暂存
$ git stash apply stash@{n}

# 恢复并删除暂存
$ git stash pop

# 删除指定暂存
$ git stash drop stash@{n}

# 清空所有暂存
$ git stash clear
```

### 5.2 部分暂存

```bash
# 只暂存指定文件
$ git stash push <file>

# 交互式选择暂存内容
$ git stash -p
```

### 5.3 暂存场景

| 场景 | 命令 |
|:-----|:-----|
| 切换分支但不想提交当前修改 | `git stash` → 切换分支 → `git stash pop` |
| 拉取远程更新前暂存本地修改 | `git stash` → `git pull` → `git stash pop` |
| 只暂存部分文件 | `git stash push <file>` |
