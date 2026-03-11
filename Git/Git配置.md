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

### 3.4 编辑器配置

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
| `core.autocrlf` | 换行符转换（Windows用`true`，Mac/Linux用`input`） | `input` |
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
