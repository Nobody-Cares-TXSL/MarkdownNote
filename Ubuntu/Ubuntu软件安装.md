# Ubuntu软件安装

## 安装 Google Chrome
```bash
# 1. 下载 Chrome deb 包
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb

# 2. 安装
sudo dpkg -i google-chrome-stable_current_amd64.deb

# 3. 如果有依赖问题，修复
sudo apt --fix-broken install

# 4. 清理安装包
rm google-chrome-stable_current_amd64.deb
```

## 安装 VS Code
- vs code: https://code.visualstudio.com/docs/setup/linux
```bash
# 安装依赖项
sudo apt-get install wget gpg

# 安装 Microsoft 签名密钥
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo install -D -o root -g root -m 644 microsoft.gpg /usr/share/keyrings/microsoft.gpg
rm -f microsoft.gpg

# 添加 VS Code 仓库
# 创建文件 /etc/apt/sources.list.d/vscode.sources
sudo nano /etc/apt/sources.list.d/vscode.sources
# 添加内容：
Types: deb
URIs: https://packages.microsoft.com/repos/code
Suites: stable
Components: main
Architectures: amd64,arm64,armhf
Signed-By: /usr/share/keyrings/microsoft.gpg

# 更新包缓存并安装 VS Code
sudo apt install apt-transport-https
sudo apt update
sudo apt install code
```

### 修复VS Code图标问题

**问题现象**：任务栏/启动器中 VS Code 图标显示为通用齿轮图标

**解决步骤**：

1. **获取正确的 WMClass 值**
   - 打开 VS Code
   - 按 `Alt + F2` 打开运行对话框
   - 输入 `lg` 并按 Enter（打开 Looking Glass）
   - 点击右上角的 "windows" 标签
   - 点击 VS Code 窗口，找到 `StartupWMClass` 的值

2. **修改 desktop 文件**
   ```bash
   sudo nano /usr/share/applications/code.desktop
   ```
   找到 `StartupWMClass=` 一行，修改为步骤1中获取的值

3. **重启电脑生效**

## 安装 Git
```bash
# 1. 更新软件包列表
sudo apt update

# 2. 安装 Git
sudo apt install git -y

# 3. 验证安装
git --version

# 配置用户名
git config --global user.name "你的名字"

# 配置邮箱
git config --global user.email "你的邮箱@example.com"

# 查看配置
git config --list
```

## 安装 Node.js
- Node.js: https://nodejs.org/en/download
```bash 
# 查看是否安装curl
curl --version
# 没有安装curl, 则安装curl
sudo apt install curl

# 1. 下载并安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash

# 2. 加载 nvm（或重启终端）
. "$HOME/.nvm/nvm.sh"

# 3. 安装 Node.js LTS 版本
nvm install 24

# 4. 验证安装
node -v      # 应显示 v24.x.x
npm -v       # 应显示 11.x.x
```

## 安装 Claude code
- Claude code: https://code.claude.com/docs/zh-CN/setup

> 原生安装已不再依赖 Node.js, 可以直接安装 Claude code CLI
```bash
# 安装 Claude code CLI
curl -fsSL https://claude.ai/install.sh | bash

# 把 ~/.local/bin 加到 PATH，并且让当前终端立刻生效
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc

# 验证安装
claude --version
```

### 配置 Claude code
- Linux 的配置文件为 `~/.claude/settings.json`
```json
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "your_zhipu_api_key",
    "ANTHROPIC_BASE_URL": "https://open.bigmodel.cn/api/anthropic",
    "API_TIMEOUT_MS": "3000000",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": 1,
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "glm-4.5-air",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "glm-4.7",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "glm-4.7"
  },
  "language": "chinese"
}
```
- 跳过新手引导: 在`~/.claude.json`中增加 `hasCompletedOnboarding` 参数    
```json
{
  "hasCompletedOnboarding": true
}
```