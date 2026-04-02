# Git 标签

> Git 可以给仓库历史中的某一个提交打上标签，以示重要。常用于标记发布版本（如 v1.0.0、v2.1.3）。

## 一、标签类型

| 类型 | 说明 | 使用场景 |
|------|------|----------|
| **附注标签** | 存储完整对象（作者、邮箱、日期、标签信息、GPG签名） | 正式发布版本 |
| **轻量标签** | 仅是指向特定提交的引用 | 临时标记、个人使用 |

---

## 二、常用命令

### 创建标签

```bash
# 附注标签（推荐）
git tag -a <tag-name> -m "commit message"

# 轻量标签
git tag <tag-name>

# 给历史提交打标签
git tag -a <tag-name> -m "commit message" <commit-hash>
```

### 查看标签

```bash
# 列出所有标签
git tag

# 搜索标签
git tag -l "<pattern>"

# 查看标签信息
git show <tag-name>

# 查看标签指向的提交
git rev-parse <tag-name>
```

### 删除标签

```bash
# 删除本地标签
git tag -d <tag-name>

# 删除远程标签（推荐）
git push <remote> --delete <tag-name>
```

### 推送标签

```bash
# 推送单个标签
git push <remote> <tag-name>

# 推送所有标签
git push <remote> --tags
```

### 检出标签

```bash
# 检出标签（会进入分离头指针状态）
git checkout <tag-name>

# 基于标签创建新分支
git switch -c <branch-name> <tag-name>

# 切回上一个分支
git switch -
```

---

## 三、进阶命令

### GPG 签名标签

> 使用 GPG（GNU Privacy Guard）对标签进行数字签名，可以验证标签的来源和完整性，防止标签被篡改。常用于开源项目的正式发布。

```bash
# 创建签名标签（需要先配置 GPG 密钥）
git tag -s <tag-name> -m "message"

# 验证签名
git tag -v <tag-name>
```

**前置配置：**

```bash
# 1. 生成 GPG 密钥（如果还没有）
gpg --full-generate-key

# 2. 列出所有 GPG 密钥，获取密钥 ID
gpg --list-keys --keyid-format=long
# 输出示例：sec   rsa4096/3AA5C34371567BD2 ...

# 3. 配置 Git 使用该 GPG 密钥
git config --global user.signingkey <gpg-key-id>
git config --global commit.gpgsign true
git config --global tag.gpgsign true

# 4. （可选）配置签名格式
git config --global gpg.format openpgp
```

### 按标签范围查看提交

```bash
# 查看两个标签之间的提交
git log <tag-a>..<tag-b>

# 查看从某个标签到现在的提交
git log <tag-name>..
```

### 创建归档

> 将指定标签的代码打包成压缩文件，用于发布源码包或备份。不包含 `.git` 目录，只包含源代码文件。

```bash
# 创建 tar.gz 压缩包
git archive -o <archive-name>.tar.gz --prefix=<dir>/ <tag-name>

# 创建 zip 压缩包
git archive -o <archive-name>.zip --format=zip <tag-name>
```

**参数说明：**

| 参数 | 说明 |
|------|------|
| `-o <file>` | 指定输出文件名 |
| `--format=<format>` | 归档格式：tar、zip、tar.gz 等 |
| `--prefix=<dir>/` | 在归档中添加目录前缀（末尾必须有 `/`）|
| `<tag-name>` | 要打包的标签或提交 |

**更多示例：**

```bash
# 不指定前缀（文件直接放在压缩包根目录）
git archive -o v1.0.0.tar.gz v1.0.0

# 指定前缀（解压后文件都在 project-v1.0.0/ 目录下）
git archive -o v1.0.0.tar.gz --prefix=project-v1.0.0/ v1.0.0

# 只打包特定目录
git archive -o api.tar.gz v1.0.0 --prefix=api/ src/api

# 使用管道压缩（更灵活）
git archive --format=tar v1.0.0 | gzip > v1.0.0.tar.gz
git archive --format=zip v1.0.0 > v1.0.0.zip

# 列出归档内容而不实际创建文件
git archive --format=tar v1.0.0 | tar -t
```

### 查看远程标签

```bash
# 列出远程仓库的所有标签
git ls-remote --tags <remote>

# 查看标签的详细信息
git show-ref --tags
```

### 批量操作

> 使用管道和 xargs 等工具对多个标签进行批量处理，提高效率。

```bash
# 批量删除本地标签
git tag | grep "<pattern>" | xargs git tag -d

# 强制覆盖远程标签（慎用）
git push <remote> +<tag-name>
```

**更多示例：**

```bash
# 批量删除匹配模式的所有本地标签
git tag | grep "v1.0.*" | xargs git tag -d

# 批量删除所有本地标签
git tag | xargs git tag -d

# 批量推送匹配的标签到远程
git tag | grep "v2.*" | xargs -I {} git push origin {}

# 批量删除远程标签（循环方式）
for tag in $(git tag | grep "temp.*"); do
    git push origin :refs/tags/$tag
done

# 推送所有未推送的标签（本地有但远程没有的）
git tag | while read tag; do
    git ls-remote --tags origin | grep -q "refs/tags/$tag$" || git push origin $tag
done

# 下载所有远程标签到本地（不合并）
git fetch origin --tags

# 删除所有远程标签（危险操作，慎用）
git tag | xargs -I {} git push origin --delete {}
```

### 同名冲突处理

```bash
# 同时存在同名标签和分支时，Git 优先检出标签
git checkout <tag-name>      # 检出标签
git checkout heads/<name>    # 明确检出分支
```

---

## 四、命名规范

### 语义化版本

> 遵循 [Semantic Versioning 2.0.0](https://semver.org/lang/zh-CN/) 规范，格式：`主版本号.次版本号.修订号[-预发布标识][+构建元数据]`

```
格式：MAJOR.MINOR.PATCH[-PRERELEASE][+BUILD]
示例：v1.2.3-beta.1+build.123
```

| 版本号 | 名称 | 说明 | 示例 |
|--------|------|------|------|
| **MAJOR** | 主版本号 | 不兼容的 API 修改 | `1.0.0` → `2.0.0` |
| **MINOR** | 次版本号 | 向下兼容的功能性新增 | `1.0.0` → `1.1.0` |
| **PATCH** | 修订号 | 向下兼容的问题修正 | `1.0.0` → `1.0.1` |
| **PRERELEASE** | 预发布标识 | alpha、beta、rc 等 | `1.0.0-alpha` |
| **BUILD** | 构建元数据 | 可选的构建信息 | `1.0.0+build.123` |

**版本号递增规则：**

```bash
# 修订版本：修复 Bug，不影响 API
v1.0.0 → v1.0.1

# 次版本：新增功能，向下兼容
v1.0.1 → v1.1.0

# 主版本：破坏性更新，不兼容旧版本
v1.1.0 → v2.0.0
```

### 常见前缀

| 前缀 | 阶段 | 说明 | 示例 |
|------|------|------|------|
| `v` | 正式版本 | 生产环境稳定版本 | `v1.0.0` |
| `alpha` | 内部测试 | 功能不完整，可能有大问题 | `v1.0.0-alpha`、`v1.0.0-alpha.1` |
| `beta` | 公开测试 | 功能完整，可能有 Bug | `v1.0.0-beta`、`v1.0.0-beta.1` |
| `rc` | 候选版本 | 即将发布，不再加新功能 | `v1.0.0-rc1`、`v1.0.0-rc2` |
| `dev/snapshot` | 开发版本 | 持续集成版本，不稳定 | `v1.0.0-dev`、`v2.0.0-SNAPSHOT` |
| `lt/lts` | 长期支持 | 长期维护版本 | `v1.0.0-lts` |

**版本发布流程：**

```
开发阶段 → alpha → beta → rc → 正式版本
v1.0.0-alpha.1 → v1.0.0-beta.1 → v1.0.0-rc1 → v1.0.0
```

**组合示例：**

```bash
# 稳定版本
v1.0.0
v2.1.3

# 预发布版本
v1.0.0-alpha
v1.0.0-alpha.1
v1.0.0-beta
v1.0.0-beta.1
v1.0.0-rc.1
v1.0.0-rc.2

# 带构建元数据
v1.0.0+20130313144700
v1.0.0-beta+exp.sha.5114f85

# 开发版本
v1.0.0-dev
v1.0.0-SNAPSHOT
v1.0.0-nightly

# 长期支持版本
v1.0.0-lts
v1.0.0-lt
```

---

## 五、最佳实践

### 标签选择策略

| 场景 | 标签类型 | 命令 |
|------|----------|------|
| 正式发布 | 附注标签 | `git tag -a <tag-name> -m "message"` |
| 临时标记 | 轻量标签 | `git tag <tag-name>` |
| 开源项目 | 签名标签 | `git tag -s <tag-name> -m "message"` |

### 标签 vs 分支

| 特性 | 标签 | 分支 |
|------|------|------|
| 指针移动 | 固定，不会改变 | 随提交自动移动 |
| 主要用途 | 标记稳定版本/里程碑 | 持续开发的代码线 |
| 删除 | 可以删除（需谨慎） | 可以删除 |
| 命名规范 | 版本号（v1.0.0） | 功能描述（feature/login） |
| 数量 | 通常较少 | 可能很多 |

### 核心建议

| 建议 | 说明 |
|------|------|
| **正式发布用附注标签** | 包含完整信息，可验证 |
| **临时标记用轻量标签** | 简单快速，方便删除 |
| **每次发布必须打标签** | 便于回溯和版本管理 |
| **标签需显式推送** | `git push` 不会推送标签 |
| **遵循语义化版本** | 使用 `MAJOR.MINOR.PATCH` 格式 |
| **标签信息要详细** | 说明版本变更内容 |
| **不要修改已推送的标签** | 删除后重新创建 |

### 发布检查清单

```bash
# 发布前检查
□ 代码已合并到主分支
□ 所有测试通过
□ 更新了 CHANGELOG
□ 版本号已更新（如 package.json、pom.xml 等）
□ 创建了附注标签并包含详细说明
□ 推送了代码和标签到远程
□ （可选）基于标签创建了发布分支
□ （可选）在 GitHub/GitLab 创建了 Release
```