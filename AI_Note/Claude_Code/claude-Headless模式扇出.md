# claude-Headless模式扇出

## Headless 模式扇出
> 利用 Claude Code 的无头模式（Headless Mode），结合脚本工具（如 xargs、GNU parallel 或 Python 脚本），**并发地**启动多个 Claude Code 实例，同时对大量文件或任务进行处理  


**以下操作在git bash中:**
```bash
# 1. 查找文件
# 2. 排除不需要的目录
# 3. 使用 xargs 并行执行，并发数控制在 3
# 4. Prompt 明确、封闭
find note -name "*.md" | \
grep -v "/Java/" | \
xargs -P 3 -I {} \
claude -p "用一句话总结 {} 文件, 并标明文件名"

# 5. 后处理：格式化代码
black src/

# 6. 验证：运行检查
flake8 src/
```