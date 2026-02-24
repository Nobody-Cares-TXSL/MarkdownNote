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

## 安装搜狗输入法
```bash
# 更新系统软件包
sudo apt update

# 卸载 Fcitx5 和 IBus（如果存在）
sudo apt remove --purge fcitx5* ibus*

# 清理系统残留
sudo apt autoremove && sudo apt autoclean

# 安装 Fcitx4 输入法框架
sudo apt install fcitx

# 设置 Fcitx 开机自启动
sudo cp /usr/share/applications/fcitx.desktop /etc/xdg/autostart/

# 设置 -> 系统 -> 区域和语言 -> 管理已安装语言 -> 增加简体中文;选择Fcitx4;应用到整个系统

# 下载搜狗输入法 deb 包 https://shurufa.sogou.com/linux
# deb格式选择
# 查看CPU架构
uname -m
# 如果输出是 x86_64，下载 “x86_64 ↓”。
# 如果是 aarch64 或类似，下载 “arm64 ↓”。
# 如果是 mips64el，下载 “mips64el ↓”。
# 如果是 loongarch64，下载 “loongarch64 ↓”。

# cd到下载目录,安装搜狗输入法
sudo dpkg -i sogoupinyin_*.deb

# 安装必要的依赖包
sudo apt install libqt5qml5 libqt5quick5 libqt5quickwidgets5 qml-module-qtquick2
sudo apt install libgsettings-qt1

# 修复可能的依赖关系问题
sudo apt install -f

# 修改配置文件
sudo nano /etc/gdm3/custom.conf
# 找到以下行并取消注释（删除行首的 #）：
# WaylandEnable=false
# 修改后应该是：
WaylandEnable=false 
```
> 设置WaylandEnable=false后, 触摸板的手势功能会失效  
> 
> 手势的三指上下滑打开应用网格可以按 `Super(Win键) + A` 打开  
> 手势的三指左右滑切换窗口可以按 `Super(Win键) + Alt + 方向键` 切换
> 
> 查看当前会话类型: `echo $XDG_SESSION_TYPE`
> 
> - 如果输出是 `x11`，说明正在使用 Xorg（Wayland 已禁用）。
> - 如果输出是 `wayland`，说明正在使用 Wayland（Wayland 已启用）。


### 不显示搜狗输入法
```bash
# 1. 检查 Fcitx 是否安装成功
fcitx --version

# 2. 配置 im-config 使用 Fcitx
im-config -n fcitx

# 3. 设置环境变量 (注意: 这里使用 zsh, 如果使用 bash, 请修改为 ~/.bashrc)
echo 'export GTK_IM_MODULE=fcitx' >> ~/.zshrc
echo 'export QT_IM_MODULE=fcitx' >> ~/.zshrc
echo 'export XMODIFIERS=@im=fcitx' >> ~/.zshrc
source ~/.zshrc

# 4. 手动启动 Fcitx 测试
fcitx -d

# 5. 如果提示有进程冲突，先杀掉
killall fcitx
fcitx -d

# 6. 打开 Fcitx 配置,将搜狗输入法调整到第一位
fcitx-configtool
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

## 安装 Claude code
- Claude code: https://code.claude.com/docs/zh-CN/setup

> 原生安装已不再依赖 Node.js, 可以直接安装 Claude code CLI
```bash
# 安装 Claude code CLI
curl -fsSL https://claude.ai/install.sh | bash

# 把 ~/.local/bin 加到 PATH，并且让当前终端立刻生效
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc

# 在shell的配置文件中添加claude环境变量

# 验证安装
claude --version
```
> [Claude 环境变量配置](./Ubuntu终端配置.md#claude-环境变量配置)

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

# 在shell的配置文件中添加nvm环境变量

# 4. 验证安装
node -v      # 应显示 v24.x.x
npm -v       # 应显示 11.x.x
```
> [Node.js 环境变量配置](./Ubuntu终端配置.md#nodejs-环境变量配置)

## 安装 MySQL
- MySQL: https://documentation.ubuntu.com/server/how-to/databases/install-mysql/

```bash
# 确保端口3306未被占用 -> 没有输出说明未被占用
sudo ss -tuln | grep 3306

# 更新软件源以确保获取最新包
sudo apt update && sudo apt upgrade -y

# 安装 MySQL Server
# Ubuntu 默认仓库安装 MySQL 8.x, MySQL 服务会自动启动
sudo apt install mysql-server -y

# 检查服务是否运行和版本
sudo systemctl status mysql # 显示 "Active: active (running)"，表示成功。
mysql --version

# 检查 MySQL 服务的网络状态
sudo ss -tap | grep mysql
```

### 配置 MySQL 服务
> MySQL默认提供的 root 用户是一个无密码的本地用户，用于管理数据库。  
> Ubuntu 的 MySQL 8+ 使用 auth_socket 插件：它**依赖 Linux 系统权限（sudo）来认证本地 root 访问**。

#### 设置密码
```bash
# 以 root 身份登录 MySQL（无需密码）
sudo mysql

# 设置密码 -> 替换your_password为你自己的密码
ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'your_password';
# 立即刷新权限表
FLUSH PRIVILEGES;

# 退出 MySQL, 测试新密码
exit
mysql -u root -p
# 输入刚才设置的密码，成功登录说明密码设置成功
```

## 安装 Redis
```bash
sudo apt update && sudo apt upgrade -y

sudo apt install redis-server -y

sudo systemctl status redis-server
redis-server --version
```

### 配置 Redis 服务
> Redis 默认用户是default，无密码的，为了安全考虑，建议设置密码。
#### 配置 Redis 密码

##### 遗留密码模式
```bash
# 编辑 Redis 配置文件
sudo nano /etc/redis/redis.conf

# 找到 requirepass 行，取消注释并设置密码
requirepass your_redis_password

# 重启 Redis 服务
sudo systemctl restart redis-server

# 测试 Redis
redis-cli

# 设置了密码，先认证：AUTH your_strong_password
AUTH your_redis_password
# 然后测试连接
ping
# 如果返回 PONG，说明连接成功
```

##### ACL 模式
> ACL配置语法结构:
>
> **user <用户名> <状态> <密码规则> <键模式> <频道模式> <命令权限>**

```bash
# 取消遗留密码模式,编辑 Redis 配置文件 `/etc/redis/redis.conf`, 注释掉 `requirepass` 行
sudo systemctl restart redis-server

# 创建 ACL 文件（如果不存在）
sudo touch /etc/redis/users.acl

# 配置 ACL 文件
sudo nano /etc/redis/users.acl
# 添加以下内容:
user default on nopass ~* &* +@all # 允许 default 用户无密码访问所有数据库
user root on >your_redis_password ~* &* +@all # 允许 root 用户使用密码访问所有数据库

# 编辑 Redis 配置文件 `/etc/redis/redis.conf`, 取消注释 `aclfile` 行, 并设置为 `/etc/redis/users.acl`

# 重启 Redis 服务, 并检查状态
sudo systemctl restart redis-server
sudo systemctl status redis-server

# 验证
redis-cli
127.0.0.1:6379> AUTH root your_redis_password
OK
127.0.0.1:6379> ping
PONG
```

## 安装 Nginx
```bash
sudo apt update && sudo apt upgrade -y

sudo apt install nginx -y

# 查看服务状态
sudo systemctl status nginx
nginx -v

# 测试 Nginx 是否正常工作
# 浏览器访问http://localhost,看到默认的 "Welcome to nginx!"，即安装完成
```

## 安装 Java JDK

```bash
sudo apt update && sudo apt upgrade -y

# 安装 OpenJDK 17 JDK
sudo apt install openjdk-17-jdk -y

# 在shell的配置文件中添加JDK环境变量

java -version
javac -version

# 查看所有已安装的 Java 版本
ls /usr/lib/jvm/
```
> [Java JDK 环境变量`JAVA_HOME`配置](./Ubuntu终端配置.md#java-jdk-环境变量java_home配置)

## 安装 Maven
```bash
sudo apt update && sudo apt upgrade -y

# 下载 Maven安装包
cd /tmp
wget https://dlcdn.apache.org/maven/maven-3/3.9.12/binaries/apache-maven-3.9.12-bin.tar.gz

# 验证文件完整性
# 下载校验文件
wget https://dlcdn.apache.org/maven/maven-3/3.9.12/binaries/apache-maven-3.9.12-bin.tar.gz.sha512

# 校验
echo "$(cat apache-maven-3.9.12-bin.tar.gz.sha512)  apache-maven-3.9.12-bin.tar.gz" | sha512sum -c -

# 解压并安装到系统目录
sudo mkdir -p /opt/maven
sudo tar -xzf apache-maven-3.9.12-bin.tar.gz -C /opt/maven --strip-components=1

# 在shell的配置文件中添加Maven环境变量

mvn -v
```
> [Maven 环境变量配置](./Ubuntu终端配置.md#maven-环境变量配置)

### 配置 Maven 阿里云镜像加速
```bash
sudo mkdir -p /opt/maven/conf
sudo cp /opt/maven/conf/settings.xml /opt/maven/conf/settings.xml.bak  # 备份

sudo nano /opt/maven/conf/settings.xml

# 找到 <mirrors> 部分替换其内容为以下内容:
<mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>阿里云公共仓库</name>
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>

# 验证配置
mvn help:system
```

### 常用 Maven 命令
```bash
mvn -v                    # 查看版本
mvn clean compile         # 清理并编译
mvn clean package         # 打包（生成 target/*.jar）
mvn spring-boot:run       # 直接运行 Spring Boot 项目
mvn archetype:generate    # 创建新项目骨架
```

## 安装 IDEA
- IDEA: https://www.jetbrains.com/idea/download/?section=linux

```bash
# 下载 IDEA tar.gz 包

# 解压到 /opt
sudo tar -xzf ideaIC-2025.2.6.1.tar.gz -C /opt

# 运行 IDE, 用全路径直接运行
/opt/idea-IC-2025.2.6.1/bin/idea.sh  # 替换为你的实际目录

# 导入配置文件 settings.zip, 并创建桌面启动器

# 切换原生启动器
vim ~/.local/share/applications/jetbrains-idea.desktop
# 将Exec行的 idea.sh 改为 idea
```

### 配置 Git
- Git 路径: `/usr/bin/git`

### 配置 Maven
- Maven主路径: `/opt/maven`
- 用户配置文件: `/opt/maven/conf/settings.xml`
> 1. 修改 `settings.xml` 中的 `<mirrors>` 部分, 配置阿里云镜像加速
>  ```xml
>  <mirror>
>         <id>aliyunmaven</id>
>         <mirrorOf>*</mirrorOf>
>         <name>阿里云公共仓库</name>
>         <url>https://maven.aliyun.com/repository/public</url>
>  </mirror>
>  ``` 
> 2. 在 `/opt/maven/` 下创建 `mvn_repo` 文件夹
> 3. 增加 `<localRepository>~/.m2/repository</localRepository>` 在 `<settings>` 标签下

- 本地仓库: `/opt/maven/mvn_repo`

## 安装 DataGrip


```bash
# 下载 DataGrip tar.gz 包

# 解压到 /opt
sudo tar -xzf datagrip-2025.3.5.tar.gz -C /opt

# 运行 IDE, 用全路径直接运行
/opt/DataGrip-2025.3.5/bin/datagrip.sh  # 替换为实际解压目录

# 创建桌面启动器

# 切换原生启动器
vim ~/.local/share/applications/jetbrains-datagrip.desktop
# 将Exec行的 datagrip.sh 改为 datagrip
```

## 安装 Jmeter
- Jmeter: https://dlcdn.apache.org/jmeter/binaries/

```bash
# 下载 Jmeter tar.gz 包
wget https://dlcdn.apache.org/jmeter/binaries/apache-jmeter-5.6.3.tgz

# 解压并安装到系统目录
sudo mkdir -p /opt/jmeter
sudo tar -xzf apache-jmeter-5.6.3.tgz -C /opt/jmeter --strip-components=1

# 在shell的配置文件中添加Jmeter环境变量

# 验证安装
jmeter -v
```
> [Jmeter 环境变量配置](./Ubuntu终端配置.md#jmeter-环境变量配置)





## 安装 ffmpeg
> 安装 ffmpeg 用于vs code的视频转GIF

```bash
sudo apt update && sudo apt upgrade -y

sudo apt install ffmpeg -y

ffmpeg -version
```