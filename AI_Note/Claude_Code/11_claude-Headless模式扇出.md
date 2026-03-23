# Claude Code Headless 模式

## 概念

> 无头模式（Headless Mode）允许通过命令行调用 Claude，结合脚本工具**并发处理**大量任务

---

## 管道进出

**标准输入输出**：与 Unix 工具链集成

```bash
# 管道输入，输出到文件
cat build-error.txt | claude -p '简要解释构建错误的根因' > output.txt

# 与其他工具组合（链式调用）
cat log.txt | claude -p '提取所有错误' | grep "ERROR"
```

---

## 输出格式

| 格式 | 用途 | 示例 |
|:-----|:-----|:-----|
| `text` | 纯文本响应（默认） | `--output-format text` |
| `json` | 完整对话日志 + 元数据 | `--output-format json` |
| `stream-json` | 实时流式输出 | `--output-format stream-json` |

```bash
# JSON 格式（含成本、耗时等元数据）
cat code.py | claude -p '分析代码 Bug' --output-format json > analysis.json

# 流式 JSON（逐条输出，适合实时处理大文件）
cat log.txt | claude -p '解析错误' --output-format stream-json
```

---

## 扇出策略

### 1. xargs 并发

```bash
# -P 4: 最多 4 个进程并发执行
# -I {}: 占位符，替换为实际文件名
find . -name "*.py" | \
xargs -P 4 -I {} \
claude -p "检查 {} 文件的安全问题" --allowedTools "Read"
```

### 2. for 循环

```bash
# 顺序执行，适合需要严格控制的任务
# --allowedTools: 限制可用工具，提高安全性
for file in $(cat files.txt); do
  claude -p "将 $file 从 React 迁移到 Vue" \
    --allowedTools "Edit,Bash(git commit *)"
done
```

### 3. 工作流

| 步骤 | 说明 |
|:-----|:-----|
| 1 | 生成任务列表（让 Claude 列出所有需要处理的文件） |
| 2 | 先在 2-3 个文件上测试，精化 prompt |
| 3 | 大规模运行，用 `--allowedTools` 限制权限 |
| 4 | 后处理：格式化、验证 |

---

## 安全提示

- **生产环境关闭 `--verbose`**，开发时开启调试
- **使用 `--allowedTools`** 限制无人值守运行的权限
- **并发数控制在 3-5**，避免过载
