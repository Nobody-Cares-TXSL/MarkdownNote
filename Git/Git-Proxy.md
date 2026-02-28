# Git 代理配置

## 一、问题诊断

| 现象 | 原因 |
|------|------|
| `git clone/push` 超时 | GitHub 受网络限制或代理配置错误 |
| `Connection closed` | 代理端口不匹配或服务未运行 |
| `ping github.com` 有丢包 | ICMP 不代表 HTTPS 通 |
| 设置代理后成功 | 代理隧道绕过限制 |

---

## 二、配置方法

### 方案 A：环境变量（推荐，灵活）

**优点**：仅当前会话生效，不影响其他操作

```bash
# HTTP/HTTPS 代理
export https_proxy=http://127.0.0.1:7890 \
       http_proxy=http://127.0.0.1:7890 \
       all_proxy=http://127.0.0.1:7890

# SOCKS5 代理（如 Clash）
export all_proxy=socks5://127.0.0.1:7890
```

### 方案 B：Git 配置（永久，针对特定域名）

**优点**：自动生效，只对指定域名生效

```ini
# 编辑 ~/.gitconfig
[http "https://github.com"]
    proxy = http://127.0.0.1:7890
[https "https://github.com"]
    proxy = http://127.0.0.1:7890
```

或用命令设置：

```bash
git config --global http.https://github.com.proxy http://127.0.0.1:7890
git config --global https.https://github.com.proxy http://127.0.0.1:7890
```

---

## 三、配置说明

```ini
# 语法：[协议 "URL匹配"]
[http "https://github.com"]
    proxy = http://代理地址:端口
```

| 配置项 | 说明 |
|--------|------|
| 只对 github.com 生效 | 其他 git 操作走直连 |
| 支持 HTTP/HTTPS/SOCKS5 | 根据代理软件类型选择 |
| 端口必须与代理软件一致 | 端口不匹配会导致连接失败 |

---

## 四、真实案例：端口不匹配

### 问题现象

```bash
$ /plugin marketplace add obra/superpowers-marketplace
Error: Failed to clone marketplace repository
Connection closed by UNKNOWN port 65535
```

### 诊断过程

```bash
# 1. 检查 git 配置
$ git config --get-regexp "proxy"
http.https://github.com.proxy=http://127.0.0.1:7897
https.https://github.com.proxy=http://127.0.0.1:7897

# 2. 测试代理连通性
$ curl -x http://127.0.0.1:7897 https://github.com --connect-timeout 3
# 无响应 → 端口 7897 服务未运行
```

### 根本原因

```
git 配置的代理端口：7897 ❌
实际运行的代理端口：7890 ✅
       端口不匹配 → 连接失败
```

### 解决方案

```bash
# 1. 取消错误的 git 配置
git config --global --unset http.https://github.com.proxy
git config --global --unset https.https://github.com.proxy

# 2. 使用环境变量设置正确端口
export https_proxy=http://127.0.0.1:7890 \
       http_proxy=http://127.0.0.1:7890 \
       all_proxy=http://127.0.0.1:7890

# 3. 重新执行操作 → 成功 ✓
```

---

## 五、验证命令

```bash
# 基础连接测试
git ls-remote https://github.com/用户名/仓库.git

# 详细连接日志（调试用）
GIT_CURL_VERBOSE=1 git ls-remote https://github.com/用户名/仓库.git

# 查看当前代理配置
git config --get-regexp "proxy"

# 测试代理连通性
curl -x http://127.0.0.1:端口 https://github.com --connect-timeout 3
```

---

## 六、常见问题

| 问题 | 解决 |
|------|------|
| 代理端口不通 | 检查代理软件是否开启，端口是否正确 |
| 配置不生效 | 确认 URL 匹配规则，检查配置是否写入 ~/.gitconfig |
| 端口配置错误 | 对比代理软件实际端口，更新配置 |
| 其他仓库也走代理 | 添加对应的 URL 配置或使用环境变量 |
| 不想用代理了 | 见下方"取消代理" |

---

## 七、取消代理

```bash
# 方案 A：删除 git 配置
git config --global --unset http.https://github.com.proxy
git config --global --unset https.https://github.com.proxy

# 方案 B：临时取消环境变量
unset https_proxy http_proxy all_proxy
```

---

## 八、快速参考

| 场景 | 推荐方案 |
|------|---------|
| 偶尔需要代理 | 环境变量 |
| 持续需要代理 | git config + 环境变量 |
| 多端口切换 | 环境变量（更灵活） |
| 某个仓库专用代理 | git config 指定 URL |
