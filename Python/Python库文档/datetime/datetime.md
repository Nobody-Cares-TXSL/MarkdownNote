# datetime

> datetime 是 Python 标准库中的日期时间处理模块，提供日期、时间、时间差、时区等功能。

| 类 | 说明 | 示例 |
|-----|------|------|
| `datetime` | 完整日期时间（年月日时分秒） | `2024-01-15 10:30:00` |
| `date` | 日期（年月日） | `2024-01-15` |
| `time` | 时间（时分秒） | `10:30:00` |
| `timedelta` | 时间差/间隔 | `3 days, 2 hours` |
| `timezone` | 时区信息 | `UTC+8`, `America/New_York` |

---

## datetime 类
> datetime 类用于处理包含日期和时间的完整时间对象。

### 导入
```python
from datetime import datetime
import datetime  # 或这样导入，使用时需要 datetime.datetime
```

### 常用方法

| 方法/属性 | 说明 | 示例 |
|----------|------|------|
| `.now()` | 获取当前时间 | 返回 datetime 对象 |
| `.isoformat()` | ISO 8601 格式字符串 | `"2024-01-15T10:30:00.123456"` |
| `.strftime(fmt)` | 自定义格式化 | 见下方格式符 |
| `.fromisoformat()` | 从 ISO 8601 字符串创建 datetime 对象 | `datetime.fromisoformat("2024-01-15T10:30:00")` |
| `.year/.month/.day` | 年/月/日 | 整数 |
| `.hour/.minute/.second` | 时/分/秒 | 整数 |

### strftime() 格式符

| 格式符 | 说明 | 示例 |
|--------|------|------|
| `%Y` | 4位年份 | `2024` |
| `%m` | 2位月份 | `01` ~ `12` |
| `%d` | 2位日期 | `01` ~ `31` |
| `%H` | 24小时制小时 | `00` ~ `23` |
| `%M` | 分钟 | `00` ~ `59` |
| `%S` | 秒 | `00` ~ `59` |
| `%F` | 等同于 `%Y-%m-%d` | `2024-01-15` |
| `%T` | 等同于 `%H:%M:%S` | `10:30:45` |

### 使用示例

```python
from datetime import datetime

# 1. 获取当前时间
now = datetime.now()
print(now)  # 2024-01-15 10:30:00.123456

# 2. ISO 8601 格式（标准格式）
iso_time = now.isoformat()
print(iso_time)  # 2024-01-15T10:30:00.123456

# 3. 自定义格式化
formatted = now.strftime("%Y-%m-%d %H:%M:%S")
print(formatted)  # 2024-01-15 10:30:00

# 4. 文件名安全格式
safe_format = now.strftime("%Y%m%d_%H%M%S")
print(safe_format)  # 20240115_103000

# 5. 解析时间字符串
dt = datetime.fromisoformat("2024-01-15T10:30:00")
print(dt.year)   # 2024
print(dt.month)  # 1
```
