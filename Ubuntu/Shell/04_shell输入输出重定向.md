# Shell 输入/输出重定向

## 三个标准流

- 文件描述符 0 通常是标准输入（STDIN）
- 文件描述符 1 是标准输出（STDOUT）
- 文件描述符 2 是标准错误输出（STDERR）

## 重定向语法

| 命令 | 说明 |
|------|------|
| `command > file` | stdout 覆盖写入文件 |
| `command >> file` | stdout 追加写入文件 |
| `command < file` | 从文件读取输入 |
| `command 2> file` | stderr 重定向到文件 |
| `command 2>> file` | stderr 追加到文件 |
| `n >& m` | 将文件描述符 n 合并到 m |
| `n <& m` | 将输入文件描述符 m 合并到 n |
| `command > file 2>&1` | stdout 和 stderr 合并写入文件 |
| `command >> file 2>&1` | stdout 和 stderr 合并追加到文件 |
| `command < file1 > file2` | 从 file1 读入，输出到 file2 |


## 合并 stdout 和 stderr（重点）

```bash
command > all.log 2>&1    # 先把 stdout 指向文件，再把 stderr 合并过去
```

**顺序很关键**，从左到右解析：

## Here Document

将多行文本作为输入传递给命令：

```bash
command << EOF
    第一行内容
    第二行内容
EOF
```

- 结尾的 `EOF` 必须**顶格写**，前后不能有空格
- 开始的 `EOF` 前后空格会被忽略

## /dev/null — 黑洞设备

```bash
command > /dev/null        # 丢弃 stdout
command > /dev/null 2>&1   # 丢弃 stdout 和 stderr（静默执行）
```

写入 `/dev/null` 的内容全部被丢弃，读取时返回空。常用于屏蔽不需要的输出。
