# Git 忽略文件

> 通过 `.gitignore` 文件指定 Git 需要忽略的文件和目录。

## 一、文件位置

| 位置 | 作用范围 |
|:-----|:---------|
| `.gitignore` | 当前仓库及子目录 |
| `~/.gitignore_global` | 全局所有仓库（需配置 `git config --global core.excludesfile`） |
| `.git/info/exclude` | 当前仓库（本地独有，不共享） |

## 二、.gitignore 规范

### 2.1 格式规则

| 规则 | 说明 | 示例 |
|:-----|:-----|:-----|
| `#` 开头 | 注释行 | `# 这是注释` |
| 空行 | 忽略 | |
| `*.log` | 匹配所有 .log 文件 | 忽略所有日志文件 |
| `/build` | 匹配根目录下的 build（不匹配子目录） | 只忽略根目录的 build |
| `build/` | 匹配 build 目录 | 忽略所有 build 目录 |
| `**/node_modules` | 匹配任意层级的 node_modules | 忽略所有 node_modules |
| `!important.log` | 取消忽略 | 即使前面忽略了 *.log，important.log 仍被跟踪 |

### 2.2 Glob 模式

| 符号 | 说明 | 示例 |
|:-----|:-----|:-----|
| `*` | 匹配任意字符（除 `/`） | `*.tmp` 匹配 `a.tmp` 但不匹配 `dir/a.tmp` |
| `**` | 匹配任意多层目录 | `a/**/z` 匹配 `a/z`、`a/b/z`、`a/b/c/z` |
| `?` | 匹配单个字符 | `file?.txt` 匹配 `file1.txt` |
| `[abc]` | 匹配其中任一字符 | `[abc].txt` 匹配 `a.txt`、`b.txt`、`c.txt` |
| `[0-9]` | 匹配范围内字符 | `file[0-9].txt` 匹配 `file1.txt` 到 `file9.txt` |
| `!` | 取反（不忽略） | `*.log` + `!important.log` |

## 三、实用命令

```bash
# 查看已被忽略的文件
git ls-files --others --ignored --exclude-standard

# 查看某个文件是否被忽略
git check-ignore -v <file>

# 调试 .gitignore（显示匹配规则）
git check-ignore -v <file>
```

## 四、参考资源

- [gitignore.io](https://www.gitignore.io/) - 在线生成 .gitignore
- [GitHub gitignore 模板集合](https://github.com/github/gitignore)
- [Git 官方文档](https://git-scm.com/docs/gitignore)
