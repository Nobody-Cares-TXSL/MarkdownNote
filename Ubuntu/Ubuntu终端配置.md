# Ubuntu 终端配置
**Alacritty + zsh + tmux**
- Alacritty: 终端模拟器,提供图形界面和环境框架
- zsh: 强大的 shell, 提供更丰富的功能和插件支持
- tmux: 终端复用工具, 劫持 Shell 的输入/输出，提供会话管理和分屏

## 安装 zsh
```bash
sudo apt update && sudo apt upgrade -y

# 安装 zsh
sudo apt install zsh -y

# 设为默认 shell
which zsh            # 比如 /usr/bin/zsh
grep -Fxq "$(which zsh)" /etc/shells || sudo sh -c 'echo "$(which zsh)" >> /etc/shells'
chsh -s "$(which zsh)"
# 重启生效

# 检查是否设为默认 shell
echo $SHELL
# 显示当前进程（真正运行的 shell）
ps -p $$ -o comm=    
```

### 安装 Oh My Zsh（Zsh 插件框架）
> Oh My Zsh 提供主题和插件支持

```bash
# 自动安装并配置 ~/.zshrc（会覆盖现有文件）
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

#### Oh My Zsh 的目录结构
```
~/.oh-my-zsh/
├── plugins/          # 官方自带插件（git、sudo 等）
└── custom/
    └── plugins/      # 用户自定义插件放这里 ← 你的插件要放这
```

#### 插件查找机制

Oh My Zsh 按以下顺序查找插件：

| 顺序 | 路径 |
|------|------|
| 1️⃣ | `~/.oh-my-zsh/custom/plugins/插件名/` |
| 2️⃣ | `~/.oh-my-zsh/plugins/插件名/` |

**流程说明：**

```bash
# 1. Oh My Zsh 安装后创建目录结构
~/.oh-my-zsh/custom/plugins/   # ← 专门放第三方插件

# 2. 将插件 clone 到这里
git clone ... ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/插件名
                           ↑
                    最终: ~/.oh-my-zsh/custom/plugins/插件名

# 3. 在 ~/.zshrc 的 plugins=(...) 中添加插件名
# Oh My Zsh 启动时自动查找并加载
```

#### 安装 Zsh 插件（autosuggestions 和 syntax-highlighting）
> 提供自动补全和高亮

```bash
# # 克隆插件到自定义插件目录
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# 编辑 ~/.zshrc 添加插件
nano ~/.zshrc
# 找到 plugins=(git) 行，替换为：
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
# 保存并退出

# 重新加载 ~/.zshrc
source ~/.zshrc
```

#### 安装 Starship（跨 Shell 提示器）
> Starship 美化提示符，支持 Git 等信息

```bash
curl -sS https://starship.rs/install.sh | sh

# 添加 Starship 到 Zsh
echo 'eval "$(starship init zsh)"' >> ~/.zshrc
# 重新加载配置
source ~/.zshrc
```

## 安装tmux
> tmux 用于多窗口管理  

```bash
# 安装 tmux
sudo apt install tmux -y

# 创建基本配置文件 ~/.tmux.conf（用 nano 编辑）
nano ~/.tmux.conf
# 添加：
# 启用鼠标操作
set -g mouse on
# 窗口从 1 开始编号
set -g base-index 1
# pane 分屏 从 1 开始编号
setw -g pane-base-index 1
# 用 prefix + | 横向分屏
bind | split-window -h
# 用 prefix + - 纵向分屏
bind - split-window -v
```

### tmux 基础操作

> **prefix** = **Ctrl+b**（所有操作先按这个前缀）

#### 会话管理（Session）

| 操作 | 命令/快捷键 |
|------|------------|
| 启动 tmux | `tmux` |
| 创建命名会话 | `tmux new -s dev` |
| 暂时离开（detach） | `prefix + d` |
| 重新连接 | `tmux attach` 或 `tmux a` |
| 列出所有会话 | `tmux ls` |
| 连接到指定会话 | `tmux attach -t 名字` |

#### 窗口管理（Window）

| 操作 | 快捷键 |
|------|--------|
| 新建窗口 | `prefix + c` |
| 切换到下一个窗口 | `prefix + n` |
| 切换到上一个窗口 | `prefix + p` |
| 切换到指定窗口 | `prefix + 数字` |
| 关闭窗口 | `prefix + &` |

#### 分屏操作（Pane）

| 操作 | 快捷键 |
|------|--------|
| 左右分屏 | `prefix + \|` |
| 上下分屏 | `prefix + -` |
| 切换 pane | `prefix + 方向键` |
| 关闭当前 pane | `prefix + x` |

## 安装 Alacritty
> Alacritty 是一个跨平台的终端模拟器，性能强劲，配置简单。

```bash
# 添加 PPA
sudo add-apt-repository ppa:aslatter/ppa -y

# 更新软件源
sudo apt update

# 安装 Alacritty
sudo apt install alacritty -y

# 检查安装版本
alacritty --version

# 安装 Fira Code 字体
sudo apt install fonts-firacode -y

# 创建配置文件
mkdir -p ~/.config/alacritty && nano ~/.config/alacritty/alacritty.toml
# 添加基本设置（如字体、透明度）:
[font]
normal.family = "Fira Code"  # 先安装字体：sudo apt install fonts-firacode -y
size = 12.0

[window]
opacity = 0.95
```

# 迁移bash配置到zsh
bash转zsh后, 需把之前在 `~/.bashrc` 中的配置, 迁移到 `~/.zshrc` 中

- Node.js 环境变量配置
```bash
# NVM 配置
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

- Claude 环境变量配置
```bash
# Claude Code PATH 配置
export PATH="$HOME/.local/bin:$PATH"
```