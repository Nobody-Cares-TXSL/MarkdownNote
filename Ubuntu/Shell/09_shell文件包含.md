# Shell 文件包含

将外部脚本引入当前脚本，复用公共代码。被包含的文件在**当前 Shell 环境**中执行（不是子进程），因此变量和函数可直接使用。

## 语法

```bash
. filename       # 点号与文件名之间有空格
# 或
source filename
```

两种写法**完全等价**。

## 实例

```bash
# utils.sh — 公共变量/函数
url="http://www.google.com"

# main.sh — 引入并使用
#!/bin/bash
. ./utils.sh              # 或 source ./utils.sh
echo "官网地址：$url"       # 直接使用被包含文件中的变量
```

## 注意事项

- 被包含的文件**不需要可执行权限**
- 被包含文件的变量和函数在当前环境中生效（非子进程）
- 推荐使用 `source`，可读性比 `.` 更好
