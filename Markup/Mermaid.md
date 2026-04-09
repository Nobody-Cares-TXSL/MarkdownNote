# Mermaid

> [Mermaid](https://mermaid.nodejs.cn/)    
> 用文本语法生成图表，GitHub / GitLab / Obsidian 原生支持。

## 一、流程图（Flowchart）

### 1.1 方向

| 关键字 | 方向 |
|:-------|:-----|
| `TD` / `TB` | 上 → 下 |
| `LR` | 左 → 右 |
| `BT` | 下 → 上 |
| `RL` | 右 → 左 |

### 1.2 节点形状

| 语法 | 形状 |
|:-----|:-----|
| `A[文本]` | 矩形 |
| `A(文本)` | 圆角矩形 |
| `A{文本}` | 菱形（判断） |
| `A((文本))` | 圆形 |
| `A{{文本}}` | 六边形 |
| `A[/文本/]` | 平行四边形 |
| `A[\文本\]` | 梯形 |
| `A[(文本)]` | 圆柱（数据库） |

### 1.3 箭头类型

| 语法 | 说明 |
|:-----|:-----|
| `-->` | 实线箭头 |
| `-.->` | 虚线箭头 |
| `==>` | 粗线箭头 |
| `---` | 实线（无箭头） |
| `-->\|标签\|` | 带标签的箭头 |
| `-- 文字 -->` | 标签的另一种写法 |

### 1.4 子图（Subgraph）

用 `subgraph` 将相关节点分组：

```mermaid
graph LR
    subgraph CI[持续集成]
        A[推送代码] --> B[运行测试]
        B --> C[构建镜像]
    end

    subgraph CD[持续部署]
        D[推送到仓库] --> E[部署到测试环境]
        E --> F{冒烟测试通过?}
        F -->|是| G[部署到生产环境]
        F -->|否| H[回滚]
    end

    C --> D
```

## 二、时序图（Sequence Diagram）

> 展示组件间随时间的通信过程，纵轴为时间（向下流动）。

### 2.1 参与者

```mermaid
sequenceDiagram
    actor 用户
    participant 客户端
    participant 服务器
    participant DB as 数据库
```

- `actor` 显示为人形图标
- `participant` 显示为系统方框
- `as` 关键字设置别名

### 2.2 消息类型

| 语法 | 说明 |
|:-----|:-----|
| `->` | 实线（同步） |
| `-->` | 虚线（返回/响应） |
| `->>` | 实线箭头（异步请求） |
| `-->>` | 虚线箭头（异步响应） |
| `-x` | 实线叉号（丢失消息） |

### 2.3 激活条

显示参与者何时在处理请求：

```mermaid
sequenceDiagram
    Client->>+Server: POST /login
    Server->>+DB: SELECT user
    DB-->>-Server: 用户记录
    Server-->>-Client: 200 OK + JWT
```

- `+` 加在箭头末尾：激活**接收方**，显示激活条（开始处理）
- `-` 加在箭头末尾：关闭**发送方**的激活条（处理完毕）

> 上例中：`Client->>+Server` 激活 Server；`DB-->>-Server` 关闭 DB 的激活条；`Server-->>-Client` 关闭 Server 的激活条

### 2.4 控制流

| 语法 | 用途 | 
|:-----|:-----|
| `alt` / `else` | 条件分支 | 
| `opt` | 可选交互 | 
| `loop` | 循环 | 
| `par` / `and` | 并行执行 | 

> 所有控制流块都必须用 `end` 关闭。

```mermaid
sequenceDiagram
    actor 用户
    participant 应用
    participant 认证服务
    participant API

    用户->>应用: 点击登录
    应用->>认证服务: POST /auth/token
    认证服务-->>应用: JWT Token

    alt Token 有效
        应用->>API: GET /data (Bearer token)
        API-->>应用: 200 OK
        应用-->>用户: 显示仪表盘
    else Token 过期
        应用->>认证服务: POST /auth/refresh
        认证服务-->>应用: 新 JWT
        应用->>API: GET /data (新 token)
        API-->>应用: 200 OK
        应用-->>用户: 显示仪表盘
    end

    par 并行请求
        应用->>API: GET /user/profile
        API-->>应用: 用户信息
    and
        应用->>API: GET /user/settings
        API-->>应用: 用户设置
    end

    loop 每 5 分钟
        应用->>API: GET /heartbeat
        API-->>应用: 200 OK
    end
```

### 2.5 注释

```mermaid
sequenceDiagram
    participant A as 服务 A
    participant B as 服务 B
    Note right of A: 最多重试 3 次
    A->>B: 请求
    Note over A,B: TLS 加密传输
    B-->>A: 响应
```

- `Note right of` / `Note left of` — 附在参与者一侧
- `Note over A,B` — 跨参与者显示

## 三、类图（Class Diagram）

> 展示面向对象系统的类结构、属性、方法及类之间的关系。

### 3.1 可见性标记

| 标记 | 访问级别 |
|:-----|:---------|
| `+` | public |
| `-` | private |
| `#` | protected |
| `~` | package / internal |

### 3.2 类定义

```mermaid
classDiagram
    class User {
        +String email
        +String name
        -String passwordHash
        +login(password) bool
        +updateProfile(data) void
        -hashPassword(raw) String
    }
```

### 3.3 类之间的关系

**连线类型：**

| 符号 | 线型 |
|:-----|:-----|
| `--` | 实线 |
| `..` | 虚线 |

**端点符号：**

| 符号 | 样式 | 含义 |
|:-----|:-----|:-----|
| `<` / `>` | 箭头 | 指向 |
| `<\|` / `\|>` | 三角 | 继承/实现 |
| `*` | 实心菱形 | 组合 |
| `o` | 空心菱形 | 聚合 |

> 端点符号可写在连线 `--` / `..` 的**任意一侧**，方向由书写位置决定。例如 `--|>` 和 `<|--` 都是继承，仅方向不同。

**常用组合：**

| 语法 | 关系 | 说明 |
|:-----|:-----|:-----|
| `<\|--` | 继承 | extends |
| `*--` | 组合 | 生命周期依赖（ owns ） |
| `o--` | 聚合 | 独立生命周期（ has ） |
| `-->` | 关联 | uses |
| `..>` | 依赖 | depends on |
| `..\|>` | 实现 | implements interface |

### 3.4 注解

用 `<<interface>>` 或 `<<abstract>>` 标注类的类型：

```mermaid
classDiagram
    class PaymentProcessor {
        <<interface>>
        +processPayment(amount) bool
        +refund(transactionId) bool
    }

    class StripeProcessor {
        +processPayment(amount) bool
        +refund(transactionId) bool
        -apiKey: String
    }

    PaymentProcessor <|.. StripeProcessor
```

### 3.5 多重性标签

在关系连线上用 `"标签"` 标注两端的数量关系：

```
类A "1" --> "*" 类B : 描述
```

| 标签 | 含义 |
|:-----|:-----|
| `"1"` | 恰好一个 |
| `"0..1"` | 零或一个 |
| `"*"` | 零或多个 |
| `"1..*"` | 一个或多个 |

> 标签写在类名与连线语法之间，第一个标签属于左边的类，第二个属于右边的类。

### 3.6 完整示例：电商领域模型

```mermaid
classDiagram
    class User {
        +String id
        +String email
        +String name
        +getOrders() Order[]
    }

    class Order {
        +String id
        +Date createdAt
        +String status
        +getTotal() number
        +cancel() void
    }

    class OrderItem {
        +int quantity
        +number price
        +getSubtotal() number
    }

    class Product {
        +String id
        +String name
        +number price
        +int stock
        +isAvailable() bool
    }

    class Cart {
        +addItem(product, qty) void
        +removeItem(productId) void
        +checkout() Order
    }

    User "1" --> "*" Order : places
    User "1" --> "1" Cart : has
    Order "1" *-- "*" OrderItem : contains
    OrderItem "*" --> "1" Product : references
    Cart "1" o-- "*" Product : holds

```

## 四、实体关系图（ER Diagram）

> 建模数据库表结构，展示实体、属性及表间关系。与 SQL 表设计一一对应。

### 4.1 实体与属性

`PK` 标记主键，`FK` 标记外键，`UK` 标记唯一约束：

```mermaid
erDiagram
    USER {
        int id PK
        string email UK
        string username UK
        string password_hash
        datetime created_at
    }
```

### 4.2 关系基数

| 语法 | 含义 |
|:-----|:-----|
| `\|\|--\|\|` | 一对一 |
| `\|\|--o{` | 一对零或多 |
| `\|\|--\|{` | 一对一或多 |
| `}o--o{` | 零或多对零或多 |

> 符号拆解：`|` 恰好一个，`o` 零个，`{` 多个。从左往右读。

```mermaid
erDiagram
    USER ||--o{ ORDER : places
    ORDER ||--|{ LINE_ITEM : contains
```

### 4.3 完整示例：博客平台

```mermaid
erDiagram
    USER {
        int id PK
        string email UK
        string username UK
        string password_hash
        datetime created_at
    }

    POST {
        int id PK
        int author_id FK
        string title
        text content
        string slug UK
        boolean published
        datetime created_at
    }

    COMMENT {
        int id PK
        int post_id FK
        int author_id FK
        text body
        datetime created_at
    }

    TAG {
        int id PK
        string name UK
        string slug UK
    }

    POST_TAG {
        int post_id FK
        int tag_id FK
    }

    USER ||--o{ POST : writes
    USER ||--o{ COMMENT : writes
    POST ||--o{ COMMENT : has
    POST ||--o{ POST_TAG : ""
    TAG ||--o{ POST_TAG : ""
```

## 五、甘特图（Gantt Chart）

> 可视化项目时间线，展示任务、工期、依赖和里程碑。

### 5.1 基本结构

```mermaid
gantt
    title 项目时间线
    dateFormat YYYY-MM-DD

    section 规划
    需求收集       :a1, 2026-04-01, 7d
    技术方案       :a2, after a1, 5d
    设计稿         :a3, after a1, 5d
```

- 每个任务可指定 `ID`（如 `a1`），供其他任务用 `after` 引用建立依赖
- 日期格式通过 `dateFormat` 指定，工期用 `Nd`（天）

### 5.2 任务状态与里程碑

| 关键字 | 效果 |
|:-------|:-----|
| `done` | 已完成（实心填充） |
| `active` | 进行中（高亮） |
| `crit` | 关键路径（红色） |
| `milestone` | 里程碑（零工期标记） |

> 状态关键字写在任务 ID 之前，逗号分隔。

### 5.3 完整示例：产品上线

```mermaid
gantt
    title 产品上线计划
    dateFormat YYYY-MM-DD

    section 开发
    核心功能           :done, dev1, 2026-04-01, 21d
    API 集成           :active, dev2, after dev1, 14d
    性能优化           :dev3, after dev2, 7d

    section 测试
    单元测试           :test1, after dev1, 14d
    集成测试           :test2, after dev2, 7d
    UAT               :crit, test3, after dev3, 5d

    section 上线
    Beta 发布          :milestone, m1, after test2, 0d
    市场准备           :launch1, after m1, 10d
    生产部署           :crit, launch2, after test3, 2d
    正式发布           :milestone, m2, after launch2, 0d
```

## 六、思维导图（Mind Map）

> 用于头脑风暴和层级化的想法整理，基于**缩进**定义层级关系。

### 6.1 基本语法

- 根节点使用 `((双圆括号))` 显示为圆形
- 子节点通过缩进定义层级
- 节点支持与[流程图](#12-节点形状)相同的形状语法：`[矩形]`、`(圆角)`、`{菱形}`、`{{六边形}}`

```mermaid
mindmap
    root((功能规划))
        UX
            用户调研
            原型设计
            可用性测试
        后端
            API 设计
            数据库设计
            认证鉴权
        测试
            单元测试
            集成测试
            压力测试
        上线
            文档
            营销页面
            更新日志
```

> 思维导图适合高层级规划和头脑风暴。如需箭头或流程方向，请使用流程图。
