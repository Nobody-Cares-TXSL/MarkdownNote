# Git 代理配置

## 快速选择

| 你的情况 | 用这个 |
|---------|--------|
| 偶尔用代理 | 环境变量 |
| 一直用代理 | git config 或 ~/.ssh/config |
| 多端口切换 | 环境变量（更灵活） |
| SSH 协议 (git@github.com) | ~/.ssh/config |
| SSH 22 端口被封 | ssh.github.com:443 |

---

## HTTPS 代理

### 环境变量（临时，推荐）

```bash
# HTTP/HTTPS 代理
export https_proxy=http://127.0.0.1:7890 \
       http_proxy=http://127.0.0.1:7890 \
       all_proxy=http://127.0.0.1:7890

# SOCKS5 代理（如 Clash）
export all_proxy=socks5://127.0.0.1:7890
```

### Git 配置（永久）

```bash
git config --global http.https://github.com.proxy http://127.0.0.1:7890
git config --global https.https://github.com.proxy http://127.0.0.1:7890
```

**取消配置**：
```bash
git config --global --unset http.https://github.com.proxy
git config --global --unset https.https://github.com.proxy
```

---

## SSH 代理

### 配置 ~/.ssh/config

```bash
# SOCKS5 代理（推荐，Clash 默认）
Host github.com
    HostName github.com
    User git
    ProxyCommand nc -X 5 -x 127.0.0.1:7890 %h %p

# HTTP 代理（二选一，上面注释掉再启用这个）
# Host github.com
#     HostName github.com
#     User git
#     ProxyCommand nc -X connect -x 127.0.0.1:7890 %h %p

# 备用：HTTPS 443 端口（22 端口被封时）
Host ssh.github.com
    Hostname ssh.github.com
    Port 443
    User git
```

**参数说明**：
| 参数 | 说明 |
|------|------|
| `-X 5` | SOCKS5 协议 |
| `-X connect` | HTTP CONNECT 协议 |
| `-x 地址:端口` | 代理服务器地址 |
| `%h %p` | 自动替换为目标主机和端口 |

**使用备用端口**：
```bash
git clone ssh://git@ssh.github.com:443/user/repo.git
```

---

## 验证命令

### HTTPS 协议

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

### SSH 协议

```bash
# 测试 SSH 连接
ssh -T git@github.com

# 测试备用 443 端口
ssh -T -p 443 git@ssh.github.com

# 查看 SSH 配置
cat ~/.ssh/config

# 详细调试日志
ssh -Tv git@github.com
```

---

## 故障排查

| 问题 | 解决 |
|------|------|
| 连接超时 | 检查代理软件是否开启、端口是否正确 |
| 配置不生效 | 检查 `~/.gitconfig` 是否写入成功 |
| 端口配置错误 | 对比代理软件实际端口，更新配置 |
| SSH 连接超时 | 检查 `~/.ssh/config` 中的 ProxyCommand 端口 |
| SSH 22 端口被封 | 使用 ssh.github.com:443 |
| nc 命令不存在 | `sudo apt install netcat` |

### 真实案例：端口不匹配

**问题**：
```bash
Connection closed by UNKNOWN port 65535
```

**诊断**：
```bash
# 检查 git 配置
git config --get-regexp "proxy"

# 测试代理连通性
curl -x http://127.0.0.1:端口 https://github.com --connect-timeout 3
```

**解决**：端口不匹配时，更新为正确端口或使用环境变量。

---

## 取消代理

```bash
# 取消 git 配置
git config --global --unset http.https://github.com.proxy
git config --global --unset https.https://github.com.proxy

# 取消环境变量
unset https_proxy http_proxy all_proxy
```
