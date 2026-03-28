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

# 设置复制快捷键 Ctrl + Insert
[[keyboard.bindings]]
key = "Insert"
mods = "Control"
action = "Copy"

# 设置粘贴快捷键 Shift + Insert
[[keyboard.bindings]]
key = "Insert"
mods = "Shift"
action = "Paste"

# 设置 Alacritty 为默认终端
sudo update-alternatives --config x-terminal-emulator
# 选择 Alacritty 作为默认终端
```

# 迁移bash配置到zsh
bash转zsh后, 需把之前在 `~/.bashrc` 中的配置, 迁移到 `~/.zshrc` 中, 迁移后需 `source ~/.zshrc`

## Node.js 环境变量配置
```bash
# NVM 配置
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

## Claude 环境变量配置
```bash
# Claude Code PATH 配置
export PATH="$HOME/.local/bin:$PATH"
```

## Java JDK 环境变量`JAVA_HOME`配置
```bash
# Java JDK 配置
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export PATH="$JAVA_HOME/bin:$PATH"
```

## Maven 环境变量配置
```bash
# Maven 配置
export MAVEN_HOME=/opt/maven
export PATH="$MAVEN_HOME/bin:$PATH"
```

## Jmeter 环境变量配置
```bash
# Jmeter 配置
export JMETER_HOME=/opt/jmeter
export PATH="$JMETER_HOME/bin:$PATH"
```

# 汉化 man 手册
> manpages-zh 提供常用 Linux 命令的中文手册页

```bash
# 安装中文手册
sudo apt install manpages-zh -y
```

## 使用说明

| 命令 | 说明 |
|------|------|
| `man 命令` | 显示中文手册（如有） |
| `man -L en 命令` | 强制显示英文手册 |
| `man -L zh_CN 命令` | 强制显示中文手册 |

**注意事项：**
- 并非所有命令都有中文翻译，部分命令仍会显示英文
- 中文翻译质量参差不齐，建议对比英文原文理解
- 如需卸载：`sudo apt remove manpages-zh`

# 终端扩展

## lazygit

```bash
# 1. 查询 lazygit 最新版本号
# 利用 GitHub API 获取最新 release 的 tag，例如 "v0.42.0"，然后去掉前面的 'v'，只保留数字版本号
LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | grep -Po '"tag_name": *"v\K[^"]*')

# 2. 下载对应版本的 lazygit 预编译二进制压缩包
# -L 让 curl 跟随重定向；-o 指定输出文件名；这里下载的是 Linux x86_64 版本的 tar.gz 包
curl -Lo lazygit.tar.gz "https://github.com/jesseduffield/lazygit/releases/download/v${LAZYGIT_VERSION}/lazygit_${LAZYGIT_VERSION}_Linux_x86_64.tar.gz"

# 3. 解压出 lazygit 可执行文件
# tar xf 表示解压文件；只解压出 lazygit 这一个文件，避免把压缩包里其他内容都展开到当前目录
tar xf lazygit.tar.gz lazygit

# 4. 将 lazygit 安装到系统命令目录
# install 命令用于安装可执行文件：
#   第一个 lazygit 是源文件
#   -D 表示必要时自动创建目标目录的父目录
#   -t /usr/local/bin/ 指定安装目录，这个目录默认就在 PATH 中，方便在任意位置直接运行 lazygit
sudo install lazygit -D -t /usr/local/bin/

# 5. 清理下载的压缩包和临时二进制文件
rm -f lazygit.tar.gz lazygit

# 6. 验证是否安装成功
# 如果正常输出版本号，说明 lazygit 已经可以被系统识别并运行
lazygit --version
```

## yazi
> yazi 是终端文件管理器

```bash
# 安装必要的依赖
sudo apt update
sudo apt install -y ffmpegthumbnailer 7zip jq poppler-utils fd-find ripgrep fzf zoxide imagemagick

# 进入下载目录
cd ~/下载

# 下载 x86_64 的 Linux 压缩包
wget https://github.com/sxyazi/yazi/releases/latest/download/yazi-x86_64-unknown-linux-gnu.zip

# 安装 unzip（如果还没装）
sudo apt install unzip -y

# 解压
unzip yazi-x86_64-unknown-linux-gnu.zip

# 进入解压后的文件夹
cd yazi-x86_64-unknown-linux-gnu

# 将 yazi 和 ya (辅助工具) 移动到 /usr/local/bin
sudo cp yazi ya /usr/local/bin/

# 给执行权限
sudo chmod +x /usr/local/bin/yazi /usr/local/bin/ya
```

### 配置 yazi
- yazi 官网文档 ： https://yazi-rs.github.io/docs/installation

```bash
nano ~/.zshrc

# 退出 yazi 后, 切换到 yazi 所在目录
function y() {
    local tmp="$(mktemp -t "yazi-cwd.XXXXXX")"
    yazi "$@" --cwd-file="$tmp"
    if cwd="$(cat -- "$tmp")" && [ -n "$cwd" ] && [ "$cwd" != "$PWD" ]; then
        builtin cd -- "$cwd"
    fi
    rm -f -- "$tmp"
}

source ~/.zshrc
```
> 之后, 可以在终端中使用 `y` 命令启动 yazi

### 修复图标显示问题
> 如果 yazi 图标显示异常, 可以尝试以下方法修复:

```bash
# 创建字体目录
mkdir -p ~/.local/share/fonts

# 进入下载目录
cd ~/下载

# 下载 FiraCode Nerd Font (只下载 Regular 和 Bold 即可，体积小)
wget https://github.com/ryanoasis/nerd-fonts/releases/latest/download/FiraCode.zip

# 解压到字体目录
unzip FiraCode.zip -d ~/.local/share/fonts

# 刷新系统字体缓存
fc-cache -fv

# 配置 alacritty 字体
nano ~/.config/alacritty/alacritty.toml

[font]
normal.family = "FiraCode Nerd Font"
bold.family = "FiraCode Nerd Font"
italic.family = "FiraCode Nerd Font"
size = 12.0
```

### 图片视频预览
```bash
# 1. 添加软件源
echo 'deb http://download.opensuse.org/repositories/home:/justkidding/xUbuntu_24.04/ /' | sudo tee /etc/apt/sources.list.d/home:justkidding.list

# 2. 添加密钥 (如果 curl 失败请检查你的代理设置)
curl -fsSL https://download.opensuse.org/repositories/home:/justkidding/xUbuntu_24.04/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/home_justkidding.gpg > /dev/null

# 3. 更新并安装
sudo apt update
sudo apt install ueberzugpp -y

# 4. 验证（现在应该能看到版本号了）
ueberzugpp --version

# 视频预览 (ffmpegthumbnailer)
sudo apt install ffmpegthumbnailer -y

# PDF 预览 (poppler-utils)
sudo apt install poppler-utils -y

# 必须做：让 Yazi 认得 ImageMagick
sudo ln -sf /usr/bin/convert /usr/local/bin/magick
```
- 配置 yazi config文件
```
[manager]
show_hidden = true

[preview]
# 关键：X11 下 Alacritty 必须用 ueberzug
preview_proto = "ueberzug"
max_width = 1200
max_height = 1200
cache_dir = "~/.cache/yazi"


[plugin]
previewers = [
    { mime = "image/*", run = "image" },
    { mime = "video/*", run = "video" },
    { mime = "application/pdf", run = "pdf" },
]
```