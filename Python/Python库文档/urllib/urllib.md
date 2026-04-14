# urllib.request

> urllib.request是Python标准库中的HTTP请求模块，用于发送网络请求、下载文件。

### 导入

```python
import urllib.request
```

### urlopen() - 发送请求

```python
with urllib.request.urlopen(url) as response:
    content = response.read()
```

| 方法 | 说明 |
|------|------|
| `response.read()` | 读取响应内容（返回 bytes） |
| `response.read().decode('utf-8')` | 读取并解码为字符串 |
| `response.status` | HTTP状态码 |
| `response.getheader(name)` | 获取响应头 |

### 使用示例

```python
import urllib.request

# 1. 获取文本内容（如README）
url = "https://raw.githubusercontent.com/user/repo/main/README.md"
with urllib.request.urlopen(url) as response:
    content = response.read().decode('utf-8')
    print(content)

# 2. 下载文件
url = "https://example.com/data.json"
urllib.request.urlretrieve(url, "local_data.json")

# 3. 添加请求头（模拟浏览器）
req = urllib.request.Request(
    url,
    headers={'User-Agent': 'Mozilla/5.0'}
)
with urllib.request.urlopen(req) as response:
    data = response.read()

# 4. 错误处理
try:
    with urllib.request.urlopen(url) as response:
        if response.status == 200:
            content = response.read().decode('utf-8')
except urllib.error.HTTPError as e:
    print(f"HTTP错误: {e.code}")
except urllib.error.URLError as e:
    print(f"URL错误: {e.reason}")
```

### GitHub Raw URL 转换

```python
# 将 GitHub URL 转换为 Raw 内容 URL
github_url = "https://github.com/user/repo"
raw_url = github_url.replace("github.com", "raw.githubusercontent.com")
# https://raw.githubusercontent.com/user/repo
```
