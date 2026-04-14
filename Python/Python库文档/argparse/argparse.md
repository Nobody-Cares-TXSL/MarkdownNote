# argparse

> argparse是Python标准库中的一个模块，用于解析命令行参数。

### 导入

```python
import argparse
```

### ArgumentParser

创建参数解析器对象。

```python
parser = argparse.ArgumentParser(
    description="程序简短描述",
    formatter_class=argparse.RawDescriptionHelpFormatter,
    epilog="""
示例文本，显示在帮助信息底部
    """
)
```

| 参数 | 说明 |
|------|------|
| `description` | 程序的简短描述，显示在帮助信息顶部 |
| `formatter_class` | 格式化器类，控制帮助信息显示格式 |
| `epilog` | 帮助信息底部的额外说明 |

### formatter_class 选项

| 格式化器 | 效果 |
|----------|------|
| `argparse.HelpFormatter` (默认) | 自动换行，多行文本会被重新排版 |
| `argparse.RawDescriptionHelpFormatter` | 保持原始格式，不会自动换行 |
| `argparse.ArgumentDefaultsHelpFormatter` | 自动添加默认值到帮助信息 |

### add_argument()

添加命令行参数定义。

```python
parser.add_argument("--info", action="store_true", help="显示信息")
parser.add_argument("--run", metavar="SCRIPT", help="运行指定脚本")
parser.add_argument("--count", type=int, default=1, help="重复次数")
```

| 参数 | 说明 | 示例 |
|------|------|------|
| 位置参数1 | 参数名称/标志 | `"--info"`, `"file"` |
| `action` | 参数动作 | `"store_true"`, `"store_false"`, `"store"` |
| `help` | 帮助文本 | `"显示信息"` |
| `metavar` | 帮助信息中的占位符 | `"SCRIPT"` → `--run SCRIPT` |
| `type` | 参数类型 | `int`, `float`, `str` |
| `default` | 默认值 | `10`, `None` |
| `required` | 是否必填 | `True`, `False` |
| `nargs` | 参数数量 | `'+'`, `'*'`, `?`, 数字 |

### action 参数类型

| action | 说明 | 示例 |
|--------|------|------|
| `"store"` (默认) | 存储值 | `--run script.py` → `args.run="script.py"` |
| `"store_true"` | 存储True，存在时为True | `--verbose` → `args.verbose=True` |
| `"store_false"` | 存储False，存在时为False | `--quiet` → `args.quiet=False` |
| `"store_const"` | 存储常量 | 需配合 `const` 使用 |
| `"append"` | 追加到列表 | 可多次使用 |

### parse_args() vs parse_known_args()

```python
# 解析所有参数，遇到未知参数报错
args = parser.parse_args()

# 解析已知参数，未知参数放入 remaining
args, remaining = parser.parse_known_args()
```

| 方法 | 未知参数处理 |
|------|--------------|
| `parse_args()` | ❌ 报错: `unrecognized arguments` |
| `parse_known_args()` | ✅ 忽略，放入 `remaining` 列表 |

### 使用示例

```python
import argparse

def main():
    parser = argparse.ArgumentParser(
        description="我的程序",
        formatter_class=argparse.RawDescriptionHelpFormatter,
        epilog="""
示例:
  python program.py --input file.txt
  python program.py --count 5 --verbose
        """
    )

    # 添加参数
    parser.add_argument("--input", help="输入文件")
    parser.add_argument("--output", help="输出文件")
    parser.add_argument("--count", type=int, default=1, help="重复次数")
    parser.add_argument("--verbose", action="store_true", help="详细输出")
    parser.add_argument("--run", metavar="SCRIPT", help="运行脚本")

    # 解析参数
    args, remaining = parser.parse_known_args()

    # 使用参数
    if args.verbose:
        print(f"处理文件: {args.input}")
        print(f"重复次数: {args.count}")

    if args.run:
        print(f"运行脚本: {args.run}")
        print(f"剩余参数: {remaining}")

    return 0

if __name__ == "__main__":
    import sys
    sys.exit(main())
```

### 常见用法模式

```python
# 1. 布尔标志
parser.add_argument("--verbose", action="store_true", help="详细输出")

# 2. 必需参数
parser.add_argument("--input", required=True, help="输入文件（必填）")

# 3. 带默认值的参数
parser.add_argument("--count", type=int, default=10, help="次数（默认10）")

# 4. 选择项
parser.add_argument("--mode", choices=["fast", "slow"], help="运行模式")

# 5. 位置参数
parser.add_argument("filename", help="文件名")
```
