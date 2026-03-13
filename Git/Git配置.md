# Git 配置

## 一、配置文件层级

Git 配置文件按优先级从高到低：

| 位置 | 作用域 | 文件路径 |
|------|--------|----------|
| **本地** | 当前仓库 | `.git/config` |
| **全局** | 当前用户 | `~/.gitconfig` 或 `~/.config/git/config` |
| **系统** | 所有用户 | `/etc/gitconfig` |

> 后读取的配置会覆盖先读取的同名配置。

## 二、查看配置

```bash
# 查看所有配置及所在文件
git config --list --show-origin

# 查看指定配置
git config <key>
# 例如：查看用户名
git config user.name

# 查看当前有效配置（不显示重复）
git config --list --show-scope
```

## 三、基础配置

### 3.1 用户信息

```bash
# 设置全局用户名和邮箱
git config --global user.name "Your Name"
git config --global user.email "your@email.com"

# 设置单个仓库的用户名和邮箱（不加 --global）
git config user.name "Your Name"
git config user.email "your@email.com"
```

### 3.2 默认分支名

```bash
# 设置默认分支名为 main
git config --global init.defaultBranch main
```

### 3.3 字符编码

#### core.quotepath

控制 Git 是否对非 ASCII 字符进行转义。

```bash
# 查看当前配置
git config core.quotepath

# 设置为 false，直接显示中文文件名
git config --global core.quotepath false
```

| 值 | 效果 |
|:---|:-----|
| `true`（默认） | 对非 ASCII 字符进行八进制转义（如 `\344\270\256`） |
| `false` | 直接显示原始字符（包括中文） |

这是 Git 对"不可打印字符"的保守处理策略。设置为 `false` 后，中文文件名可以正常显示。

### 3.4 换行符处理

| 系统 | 换行符 | 表示 |
|:-----|:-------|:-----|
| Linux/Mac | LF | `\n` |
| Windows | CRLF | `\r\n` |

跨平台协作时，换行符自动转换会导致文件显示"已修改"。

#### 方案一：core.autocrlf

```bash
# Linux/Mac 推荐
git config --global core.autocrlf input

# Windows 推荐
git config --global core.autocrlf true

# 禁用自动转换（配合 .gitattributes 使用）
git config --global core.autocrlf false
```

| 值 | 提交到仓库 | 检出到工作区 | 适用场景 |
|:---|:------|:------|:---------|
| `true` | CRLF → LF | LF → CRLF | Windows 独立开发 |
| `input` | CRLF → LF | 保持不变 | Linux/Mac 开发 |
| `false` | 不转换 | 不转换 | 配合 .gitattributes |

#### 方案二：.gitattributes（推荐）

项目级统一规范，优先级高于 `core.autocrlf`。

在项目根目录创建 `.gitattributes`：

```
* text=auto eol=lf          # 文本文件统一用 LF
*.bat text eol=crlf         # Windows 脚本必须用 CRLF
*.png binary                # 二进制文件不转换
```

| 属性 | 含义 |
|:-----|:-----|
| `text=auto` | 自动判断是否为文本文件 |
| `eol=lf/crlf` | 强制使用指定换行符 |
| `binary` | 二进制文件，不转换 |

配合 `.gitattributes`，设置 `core.autocrlf false` 禁用自动转换。

```bash
# 规范化已有文件
git add --renormalize .
git commit -m "chore: normalize line endings to LF"

# 验证文件换行符
git ls-files --eol README.md
```

### 3.5 编辑器配置

```bash
# 设置默认编辑器为 Vim
git config --global core.editor vim

# 设置为 VS Code
git config --global core.editor "code --wait"

# 设置为其他编辑器
git config --global core.editor nano
```

## 四、常用配置项

| 配置项 | 说明 | 示例值 |
|:-------|:-----|:-------|
| `user.name` | 用户名 | `Your Name` |
| `user.email` | 用户邮箱 | `your@email.com` |
| `core.editor` | 默认编辑器 | `vim` |
| `core.quotepath` | 是否转义非ASCII字符 | `false` |
| `init.defaultBranch` | 默认分支名 | `main` |
| `core.autocrlf` | 换行符转换 | `false`（配合.gitattributes） |
| `push.default` | 默认推送行为 | `simple` |

## 五、获取帮助

```bash
# 获取命令帮助
git help <command>
git <command> --help
# 例如：
git help config

# 获取简明参考帮助
git <command> -h
# 例如：
git config -h
```
