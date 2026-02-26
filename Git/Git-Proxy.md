# Git 代理配置

## 一、问题诊断

| 现象 | 原因 |
|------|------|
| git push 超时/失败 | GitHub 受网络限制 |
| ping github.com 有丢包 | ICMP 不代表 HTTPS 通 |
| 设置代理后成功 | 代理隧道绕过限制 |

## 二、配置方法

### 临时方案（单次有效）

```bash
export https_proxy=http://127.0.0.1:7897 \
       http_proxy=http://127.0.0.1:7897 \
       all_proxy=socks5://127.0.0.1:7897
```

### 永久方案（推荐）

```ini
# 编辑 ~/.gitconfig
[http "https://github.com"]
    proxy = http://127.0.0.1:7897
[https "https://github.com"]
    proxy = http://127.0.0.1:7897
```

## 三、配置说明

```ini
# 语法：[协议 "URL匹配"]
[http "https://github.com"]
    proxy = http://代理地址:端口
```

- 只对 github.com 生效
- 其他 git 操作走直连
- 支持 HTTP/HTTPS/SOCKS5 代理

## 四、验证命令

```bash
# 基础连接测试
git ls-remote https://github.com/用户名/仓库.git

# 详细连接日志
GIT_CURL_VERBOSE=1 git ls-remote https://github.com/用户名/仓库.git

# 查看当前配置
git config --get-regexp "proxy"
```

## 五、常见问题

| 问题 | 解决 |
|------|------|
| 代理端口不通 | 检查代理软件是否开启 |
| 配置不生效 | 确认 URL 匹配规则 |
| 其他仓库也走代理 | 添加对应的 URL 配置 |

## 六、取消代理

```bash
# 删除配置
git config --unset http.https://github.com.proxy
git config --unset https.https://github.com.proxy
```
