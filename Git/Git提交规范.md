# Git 提交规范

## 提交信息结构规范

采用 **约定式提交（Conventional Commits）** 规范，格式如下：

```
<type>([optional scope]): <description>

[optional body]

[optional footer(s)]
```

### 格式说明

| 部分 | 说明 | 示例 |
|:-----|:-----|:-----|
| **type** | 必填，提交类型（见下方类型说明） | `feat`、`fix`、`docs` |
| **scope** | 可选，影响范围（如组件名、模块名） | `auth`、`api`、`ui` |
| **description** | 必填，简短描述（**不超过 50 字符**） | `添加用户登录功能` |
| **body** | 可选，详细描述（**说明做了什么，为什么**） | `- 修复了密码验证逻辑<br>- 添加了 token 过期处理` |
| **footer** | 可选，关联 Issue 或破坏性变更 | `Closes #123`、`BREAKING CHANGE:` |

---

## Type 类型说明

| 类型 | 说明 | 示例 |
|:-----|:-----|:-----|
| **feat** | 新功能（Feature） | `feat: 添加用户头像上传功能` |
| **fix** | 修复 Bug | `fix: 修复登录时 token 未保存的问题` |
| **docs** | 文档更新（Documentation） | `docs: 更新 API 接口文档` |
| **style** | 代码格式调整（不影响逻辑） | `style: 统一缩进为 2 空格` |
| **refactor** | 重构（既不是新功能也不是修复） | `refactor: 重构用户服务层代码` |
| **perf** | 性能优化（Performance） | `perf: 优化数据库查询减少索引扫描` |
| **test** | 测试相关 | `test: 添加用户模块单元测试` |
| **chore** | 构建过程或辅助工具变动 | `chore: 升级 webpack 到 v5` |
| **ci** | CI/CD 配置文件和脚本 | `ci: 添加 GitHub Actions 工作流` |
| **revert** | 回滚之前的提交 | `revert: 回滚 commit abc123` |
| **build** | 打包 | `build: 打包前端代码` |

## Footer 详解

Footer 用于补充提交的元数据，主要用于 **关联 Issue** 和 **声明破坏性变更**。

### Issue 关键词

| 关键词 | 说明 | 平台支持 |
|:-----|:-----|:---------|
| **Closes** | 关闭 Issue（提交合并后自动关闭） | GitHub、GitLab |
| **Fixes** | 修复 Issue（同 Closes） | GitHub、GitLab |
| **Resolves** | 解决 Issue（同 Closes） | GitHub、GitLab |
| **Refs** | 关联 Issue（不自动关闭） | GitHub、GitLab |
| **See** | 引用 Issue（仅做参考） | GitHub |

### 破坏性变更（BREAKING CHANGE）

当提交包含 **不兼容的 API 变更** 或 **影响现有功能** 时，必须使用 `BREAKING CHANGE:` 声明。

> **重要**：`BREAKING CHANGE:` 后必须跟空格，且可以与任意 Type 组合使用（不限于 `feat`）

| 场景 | 示例 |
|:-----|:-----|
| **删除 API** | `BREAKING CHANGE: 删除了 /api/v1/user 接口` |
| **修改函数签名** | `BREAKING CHANGE: formatDate() 参数从时间戳改为 Date 对象` |
| **配置项变更** | `BREAKING CHANGE: config.db.host 改为 config.database.host` |

### Footer 格式规范

```
<BREAKING CHANGE: <详细说明>

<关键词> #<issue-number>
```

| 规则 | 说明 |
|:-----|:-----|
| **位置** | 必须在 Body 之后，与 Body 之间空一行 |
| **BREAKING CHANGE** | 冒号后必须有空格，说明内容可多行 |
| **Issue 关键词** | 区分大小写，后跟 `#` 和 Issue 编号 |