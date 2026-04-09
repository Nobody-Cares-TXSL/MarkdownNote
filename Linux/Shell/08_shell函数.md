# Shell 函数

> 函数必须**先定义后调用**。Shell 中 0 代表 true，非 0 代表 false。

---

## 定义与调用

```bash
# 三种定义方式等价
function foo() { commands; }   # 标准
function foo  { commands; }   # 省略括号
foo()         { commands; }   # 省略 function
```

调用时直接写函数名：`foo`

---

## 返回值

### return（退出码）

```bash
check() {
    # return 只能返回 0-255 的整数
    return 0    # 成功
    return 1    # 失败
}

check
echo $?        # 获取返回值（必须在函数调用后立即读取）
```

**注意：** `$?` 只保存上一条命令的状态，下一条命令会覆盖它。

### echo（输出值）

> 需要返回字符串或大于 255 的值时，用 echo 输出，调用时用 `$()` 捕获。

```bash
add() {
    echo $(( $1 + $2 ))
}

result=$(add 3 5)
echo $result   # 8
```

---

## 函数参数

> 函数内通过 `$1, $2, ...` 获取参数，与脚本参数机制相同。

```bash
funWithParam() {
    echo "第一个参数: $1"
    echo "第二个参数: $2"
    echo "第十个参数: ${10}"   # n>=10 必须用 ${n}
    echo "参数个数: $#"
    echo "所有参数: $@"
}

funWithParam a b c d e f g h i j k
```

### 参数相关变量

| 变量 | 说明 |
|------|------|
| `$#` | 参数个数 |
| `$*` | 所有参数（作为单个字符串） |
| `$@` | 所有参数（每个参数独立，保留引号边界） |
| `$?` | 上一条命令/函数的退出状态 |
| `$$` | 当前进程 PID |
| `$!` | 最后一个后台进程 PID |

### `$*` vs `$@` 区别

```bash
show() {
    echo '--- $* ---'
    for arg in "$*"; do echo "$arg"; done    # 所有参数作为一个整体
    echo '--- $@ ---'
    for arg in "$@"; do echo "$arg"; done    # 每个参数独立迭代
}

show "a b" c d
# --- $* ---
# a b c d          （1 条）
# --- $@ ---
# a b              （3 条）
# c
# d
```

---

## 函数作条件判断

```bash
is_root() {
    [ "$(id -u)" -eq 0 ]
}

if is_root; then
    echo "是 root 用户"
else
    echo "非 root 用户"
fi
```

> 函数返回 0 → 条件为 true；返回非 0 → 条件为 false。
