# Git Note

+ [Git文档](https://git-scm.com/book/zh/v2)  
+ [Git练习网站](https://learngitbranching.js.org/?locale=zh_CN)  
  **在 Git Base中复制粘贴:**
* ​**复制**​: 使用 *Ctrl + Insert* 快捷键可以复制选中的文本。
* ​**粘贴**​: 使用 *Shift + Insert* 快捷键可以粘贴剪贴板中的内容。
  
## 一. 配置
  
### *1.1* 查看Git所有的配置以及它们所在的文件
  
  ```bash
  $ git config --list --show-origin
  ```
  
### *1.2* 设置你的用户名和邮件地址
  
  ```bash
  $ git config --global user.name "NobodyCares"
  $ git config --global user.email 3226234796@qq.com
  ```
  
  > 使用了 `--global` 选项，那么该命令只需要运行一次，因为之后无论你在该系统上做任何事情， Git 都会使用那些信息
  
### *1.3* 通过输入 `git config <key>`： 来检查 Git 的某一项配置：
  
  ```bash
  $ git config user.name
  NobodyCares
  ```
  
### *1.4* 获取帮助
  
  ```bash
  $ git help <Key>
  $ git <Key> --help
  // 例如获取git config 命令手册
  $ git help config
  // 获取简明参考帮助
  $ git config -h
  ```
  
## 二. Git基础
  
### *2.1* 初始化仓库
  
  ```bash
  # 在项目中,打开Git Base
  $ git init
  ```
  
  > 该命令将创建一个名为 `.git` 的子目录，这个子目录含有你初始化的 Git 仓库中所有的必须文件，这些文件是 Git 仓库的骨干。 但是，在这个时候，我们仅仅是做了一个初始化的操作，你的项目里的文件还没有被跟踪。
  
### *2.2* 克隆仓库`git clone `
  
  例如克隆 https://github.com/H-TXSL/Airplane_Games.git
  
  ```
  $ git clone https://github.com/H-TXSL/Airplane_Games.git
  ```
  
  > 这会在当前目录下创建一个名为 “Airplane_Games” 的目录，并在这个目录下初始化一个 `.git` 文件夹， 从远程仓库拉取下所有数据放入 `.git` 文件夹，然后从中读取最新版本的文件的拷贝。  
  > Git 支持多种数据传输协议。  
  > 当你克隆一个远程仓库时，Git 通常会自动将该远程仓库命名为 origin
  
### *2.3* 忽略文件
  
  + [.gitignore 模板集合](https://github.com/github/gitignore)
  
  - 创建一个名为 `.gitignore` 的文件，列出要忽略的文件的模式

文件 `.gitignore` 的格式规范如下：

* 所有空行或者以 `#` 开头的行都会被 Git 忽略。
* 可以使用标准的 glob 模式匹配，它会递归地应用在整个工作区中。

> 所谓的 glob 模式是指 shell 所使用的简化了的正则表达式

* 匹配模式可以以（`/`）开头防止递归。
* 匹配模式可以以（`/`）结尾指定目录。
* 要忽略指定模式以外的文件或目录，可以在模式前加上叹号（`!`）取反。

> 星号（`*`）匹配零个或多个任意字符；
> `[abc]` 匹配任何一个列在方括号中的字符 （这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；
> 问号（`?`）只匹配一个任意字符；
> 如果在方括号中使用短划线分隔两个字符， 表示所有在这两个字符范围内的都可以匹配（比如 `[0-9]` 表示匹配所有 0 到 9 的数字）。
> 使用两个星号（`**`）表示匹配任意中间目录，比如 `a/**/z` 可以匹配 `a/z` 、 `a/b/z` 或 `a/b/c/z` 等

```
# .gitignore 文件示例
# 忽略所有的 .a 文件
*.a

# 但跟踪所有的 lib.a，即便你在前面忽略了 .a 文件
!lib.a

# 只忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO
/TODO

# 忽略任何目录下名为 build 的文件夹
build/

# 忽略 doc/notes.txt，但不忽略 doc/server/arch.txt
doc/*.txt

# 忽略 doc/ 目录及其所有子目录下的 .pdf 文件
doc/**/*.pdf
```

### *2.4* 记录每次更新到仓库

- 工作目录下的每一个文件都不外乎这两种状态：**已跟踪** 或 **未跟踪**

> `Untracked files` 下为**未跟踪**  
> `Changes not staged for commit` 下，说明**已跟踪文件的内容发生了变化，但还没有放到暂存区**。  
> `Changes to be committed`下为**处于暂存区**

#### *2.4.1* 检查当前文件状态

```
$ git status
```

简洁输出

```
$ git status -s
# 新添加的未跟踪文件前面有 `??` 标记
# 新添加到暂存区中的文件前面有 `A` 标记
# 修改过的文件前面有 `M` 标记
# 左栏指明了暂存区的状态，右栏指明了工作区的状态
```

#### *2.4.2* 跟踪新文件或暂存已修改的文件

```
$ git add <file>
```

#### *2.4.3* 查看已暂存和未暂存的修改

> git diff 本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动

- **比较工作区文件与暂存区文件的差异**

```
$ git diff
# 此命令比较的是工作目录中当前文件和暂存区域快照之间的差异
```

- **比较暂存区文件与本地Git仓库的差异**

```
$ git diff --staged （ `--staged` 和 `--cached` 是同义词）
# 此命令将比对已暂存文件与最后一次提交的文件差异
```

#### *2.4.4* 提交更新

```
$ git commit -m "要输入的提交说明"
```

- **跳过使用暂存区域**

> 给 `git commit` 加上 `-a` 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add` 步骤

```
$ git commit -a -m "要输入的提交说明"
```

#### *2.4.5* 移除文件

- **移除工作区文件**

```
# 直接移除
$ rm <file>
# # 工作目录中删除文件，同时把删除动作记录到暂存区
$ git rm <file>
```

> `git rm` 命令后面可以列出文件或者目录的名字，也可以使用 `glob` 模式

- **移除暂存区文件**
  
  ```
  # 从暂存区移除文件, 但不删除工作区文件
  $ git rm --cached <files>
  ```
- **移除Git仓库文件**
  
  ```
  $ git rm <file>
  $ git commit -m "移除信息说明"
  ```
  
  #### *2.4.6* 移动或重命名文件
  
  > `git mv`命令会自动跟踪文件
  
  ```
  # 将 name_1 重命名为 name_2
  $ git mv <name_1> <name_2>
  ```
  
  运行 `git mv` 就相当于运行了下面三条命令：

```
$ mv README.md README
$ git rm README.md
$ git add README
```

### *2.5* 查看提交历史

- `$ git log`命令
- [`git log` 的常用选项](https://git-scm.com/book/zh/v2/ch00/log_options)
- [限制 `git log` 输出的选项](https://git-scm.com/book/zh/v2/ch00/limit_options)

> 不传入任何参数的默认情况下，`git log` 会按时间先后顺序列出所有的提交，最近的更新排在最上面。

#### *2.5.1* 常用选项

##### *2.5.1.1* 显示每次提交引入的差异
  
  ```
  $ git log -p -number
  # `-number` 选项来只显示最近的number次提交
  ```
##### *2.5.1.2* 快速把握每次提交对文件的修改概况
  
  ```
  $ git log --stat
  # 修改增删汇总
  ```
##### *2.5.1.3* 使用不同于默认格式的方式展示提交历史
  
  ```
  $ git log --pretty=?
  # ?为子选项:
  # oneline 会将每个提交放在一行显示
  # short 作者和提交信息
  # full 作者,提交者,提交信息
  # fuller 作者,提交者,提交作者和提交者的时区信息, 提交信息
  ```
##### *2.5.1.4* 定制记录的显示格式
- [`git log --pretty=format` 常用的选项](https://git-scm.com/book/zh/v2/ch00/pretty_format)
  
  ```
  # 例如仅输出: 作者名 - 作者的电子邮件地址
  $ git log --pretty=format:"%an - %ae"
  ```
- 隐藏合并提交

> 按照你代码仓库的工作流程，记录中可能有为数不少的合并提交，它们所包含的信息通常并不多。 为了避免显示的合并提交弄乱历史记录，可以为 `log` 加上 `--no-merges` 选项。
#### *2.5.2* 查看各个分支当前所指的对象
```
# 提供这一功能的参数是 --decorate
$ git log --oneline --decorate
```
- 项目分叉历史
```
$ git log --oneline --decorate --graph --all
# 输出你的提交历史、各个分支的指向以及项目的分支分叉情况。
```


### *2.6* 撤销操作

#### *2.6.1* 修改提交信息

```
$ git commit --amend
# 进入文本编译器中修改提交信息
```

> 例如 Vim: 按 `i` 键进入插入模式,对提交信息进行修改。修改完成后，按 `Esc` 键退出插入模式，然后输入 `:wq` 并回车，这会保存修改并退出 Vim

#### *2.6.2* 添加遗漏文件到暂存区的信息到最近一次提交
  
  ```
  # 先运行 git add <file>
  # 再运行 git commit --amend
  ```
  
  > 例如Vim: 这里不用修改提交信息内容，直接按 `Esc` 键退出插入模式（如果处于插入模式的话），然后输入 `:wq` 并回车
#### *2.6.3* 取消暂存文件
  
  ```
  $ git reset HEAD <file>
  ```
#### *2.6.4* 撤销对文件的修改

> 将文件还原成上次提交时的样子,Git 会用最近提交的版本覆盖掉它

```
$ git checkout -- <file>
```
### *2.7* 远程仓库的使用
#### *2.7.1* 添加远程仓库  
```
$ git remote add origin <url>
# 起别名
$ git remote add <shortname> <url> 
# 可以在命令行中使用字符串 <shortname> 来代替整个 URL

# -u：该选项是 --set - upstream 的缩写。它的作用是在推送本地分支到远程仓库的同时，设置本地分支跟踪远程分支。
$ git push -u origin "master"

# 更改远程仓库 origin 的 URL 地址
$ git remote set-url origin <url>
```
#### *2.7.2* 查看远程仓库
```
# 显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL
$ git remote -v

# 列出远程仓库的 URL 与跟踪分支的信息
$ git remote show <remote>
```
#### *2.7.3* 从远程仓库中抓取与拉取
- **抓取**
```
# 这个命令会访问远程仓库，从中拉取所有你还没有的数据
$ git fetch <remote>
```
> 注意 git fetch 命令只会将数据下载到你的本地仓库——它并不会自动合并或修改你当前的工作  

当抓取到新的远程跟踪分支时，本地不会自动生成一份可编辑的副本（拷贝）,只有一个不可以修改的 \<remote>/\<branch> 指针  
可以运行 git merge \<remote>/\<branch> 将这些工作合并到当前所在的分支。
- **拉取**
```
# 这个命令会自动将从远程仓库抓取的分支合并到当前所在的分支
$ git pull <remote> <branch>
```

如果想要在自己的 branch 分支上工作，可以将其建立在远程跟踪分支之上
```
$ git checkout -b <branch> <remote>/<branch>
# 这会给你一个用于工作的本地分支，并且起点位于远程分支<remote>/<branch>
# 这会使创建的分支为跟踪分支
# 跟踪分支是与远程分支有直接关系的本地分支。 
# 如果在一个跟踪分支上输入 git pull，Git 能自动地识别去哪个服务器上抓取、合并到哪个分支。
# 跟踪分支的一个重要特性是，它们会自动与远程分支保持同步。
```
#### *2.7.4* 推送到远程仓库
```
$ git push <remote> <branch>
# 例如
$ git push origin master
# 推送所有分支
$ git push <remote> --all

# 推送一个分支到远程仓库，并重命名远程分支为 <branch_new_name>
# 如果远程仓库不存在 <branch_new_name> 分支，会创建一个新分支；若已存在同名分支，则会用本地分支的提交更新远程分支。
$ git push <remote> <branch>:<branch_new_name>

```
> 只有当你有所克隆服务器的写入权限，并且之前没有人推送过时，这条命令才能生效
#### *2.7.5* 远程仓库重命名与移除
```
# 重命名
$ git remote rename <old_name> <new_name>
# 移除别名
$ git remote remove <name>
或
$ git remote rm <name>
```
> 一旦你使用这种方式删除了一个远程仓库，那么所有和这个远程仓库相关的远程跟踪分支以及配置信息也会一起被删除  
### *2.8* 打标签
> <u>Git 可以给仓库历史中的某一个提交打上标签，以示重要。</u>  
> 标签在 Git 中是全局的，并不属于特定的某个分支。

> Git 支持两种标签：**轻量标签**（lightweight）与 **附注标签**（annotated）  
> **轻量标签**很像一个不会改变的分支——它只是某个特定提交的引用。  
> **附注标签**是存储在 Git 数据库中的一个完整对象， 它们是可以被校验的，其中包含打标签者的名字、电子邮件地址、日期时间， 此外还有一个标签信息，并且可以使用 GNU Privacy Guard （GPG）签名并验证。  

> 默认给当前 HEAD 所指向的提交打标签,也就是最近的一次提交
#### *2.8.1* 创建标签
- **附注标签**  
```
$ git tag -a <tag-name> -m "标签信息"
```
- **轻量标签**
> 创建轻量标签，不需要使用 -a、-s 或 -m 选项，只需要提供标签名字
```
$ git tag <tag-name>
```
#### *2.8.2* 列出标签
```
$ git tag
# (可带上可选的 -l 选项 --list)
```
> 如果你提供了一个匹配标签名的通配模式，那么 -l 或 --list 就是强制使用的  
```
$ git tag -l "v1.8.5*"
# 列出所有以 v1.8.5 开头的标签
```
#### *2.8.3* 查看标签信息
```
$ git show <tag-name>
```
#### *2.8.4* 后期打标签
可以对过去的提交打标签
```
# # <commit-id> 可以是提交的 SHA-1 校验和的前几位字符
# 打轻量标签
$ git tag <tag-name> <commit-id>
# 打附注标签
# 直接添加标签信息
$ git tag -a <tag-name> -m "标签信息" <commit-id> 
# 在文本编辑器中编辑标签信息
$ git tag -a <tag-name> <commit-id>
```
- 例如在 Vim 中
>  按 `insert` 键进入插入模式,填写标签信息。完成后，按 `Esc` 键退出插入模式，然后输入 `:wq` 并回车，这会保存修改并退出 Vim
#### *2.8.5* 共享标签
> 默认情况下，git push 命令并不会传送标签到远程仓库服务器上。  
> 创建完标签后你必须显式地推送标签到共享服务器上。  
```
$ git push <remote> <tag-name>
# 推送所有标签到远程仓库
$ git push <remote> --tags
```
> 使用 git push <remote> --tags 推送标签并不会区分轻量标签和附注标签， 没有简单的选项能够让你只选择推送一种标签。
#### *2.8.6* 删除标签
- 删除本地标签
```
$ git tag -d <tag-name>
```
- 删除远程标签
```
$ git push <remote> :refs/tags/<ta-name>
# 上面这种操作的含义是，将冒号前面的空值推送到远程标签名，从而高效地删除它。
# 更直观的删除
$ git push <remote> --delete <tag-name>
```
#### *2.8.7* 检出标签
查看某个标签所指向的文件版本
> 这会使你的仓库处于**分离头指针 detached HEAD**的状态  
> 这种状态下，你对文件所做的修改和提交不会影响到任何分支，因为 HEAD 没有与分支关联。
```
$ git checkout <tag-name>
```
- 撤销分离头指针状态
```
$ git switch -
```
- 创建新分支保留提交
> 如果你想基于当前处于 “detached HEAD” 状态下的提交创建一个新分支来保留所做的更改
```
# -c 选项的作用是创建并切换到一个新分支
$ git switch -c <new-branch-name>
```
## 三. 分支
### Git保存数据的方式
> Git 保存的不是文件的变化或者差异，而是一系列不同时刻的**快照**。   
- 提交对象
> 进行提交操作时，Git 会保存一个提交对象（commit object）。  
> 提交对象包含了**作者的姓名和邮箱、提交时输入的信息以及指向它的父对象的指针和一个指向暂存内容快照的指针**。  
> 首次提交产生的提交对象没有父对象，普通提交操作产生的提交对象有一个父对象， 而由多个分支合并产生的提交对象有多个父对象。

使用 git commit 进行提交操作时:  
1. Git 会先计算每一个子目录的**校验和**  
2. 然后将 Git 仓库中这些校验和保存为**树对象**  
3. 随后，Git 便会创建一个**提交对象**， 它除了包含上面提到的那些信息外，还包含指向这个树对象的指针。
> Git 仓库中有五个对象：三个 blob 对象（保存着文件快照）、一个 树 对象 （记录着目录结构和 blob 对象索引）以及一个 提交 对象（包含着指向前述树对象的指针和所有提交信息）。
### *3.0* HEAD指针和^,~引用
#### 分离头指针
> 分离头指针是一种特殊的状态， 它表示当前 Git 仓库处于一个没有任何分支的状态。
```
# 例如:
$ git checkout <commit-id>
# 这会使你的仓库处于分离头指针状态。
# 此时，你对文件所做的修改和提交不会影响到任何分支，因为 HEAD 没有与分支关联。
$ git switch -
# 这会将 HEAD 指针切换回上一次所在的分支，撤销进入 “分离头指针” 状态的操作,恢复到之前的工作状态。
```
#### 相对引用
> **单一父提交**的引用为上一次的提交  
> **多父提交**的引用,第一个父提交是当前分支在合并前的状态，第二个父提交是被合并分支的状态 。
- ^引用  

[![pVSC8JO.png](https://s21.ax1x.com/2025/05/24/pVSC8JO.png)](https://imgse.com/i/pVSC8JO)
```
# 例如: 现在提交记录为上图
# 现在: HEAD指向master,master指向C3
$ git checkout master^
# 这会将 HEAD 指针切换到 master 分支的上一个提交，即 c1。
$ git merge main
# 这会将当前分支（master）与 main 分支合并。
# 合并完成后，HEAD 指针会指向最新的提交，即 c4。
# 如下图所示:
```
[![pVSCtQH.png](https://s21.ax1x.com/2025/05/24/pVSCtQH.png)](https://imgse.com/i/pVSCtQH)
```
$ git checkout master^
# 这会将 HEAD 指针切换到 master 分支合并前的状态提交，即 c3。
# 如下图所示:
```
[![pVSihGT.png](https://s21.ax1x.com/2025/05/24/pVSihGT.png)](https://imgse.com/i/pVSihGT)
```
$ git checkout master
# 这会将 HEAD 指针切换回 master 分支，恢复到之前的工作状态。
$ git checkout master^2
# 这会将 HEAD 指针切换到被合并分支的状态提交，即 c2。
# 也就是之前master分支合并的main分支指向的c2提交
# 如下图所示:
```
[![pVSioM4.png](https://s21.ax1x.com/2025/05/24/pVSioM4.png)](https://imgse.com/i/pVSioM4)

- ~引用
> 相对引用的简化形式,可以使用 ~ 符号来代替 ^ 符号  

[![pVSFPeA.png](https://s21.ax1x.com/2025/05/24/pVSFPeA.png)](https://imgse.com/i/pVSFPeA)
```
# 例如分支提交为上图
# 现在: HEAD指向main,main指向C3
$ git checkout main~number
# number 为 2 时
$ git checkout main~2
# 这会将 HEAD 指针切换到 main 分支的上两个提交，即 c1。
# number 为 3 时
$ git checkout main~3
# 这会将 HEAD 指针切换到 main 分支的上三个提交，即 c0。
```

### *3.1* 分支简介
- Git 的分支，其实本质上仅仅是指向提交对象的可变指针。
- Git 的默认分支名字是 master, 是因为 git init 命令默认创建它
### *3.2* 分支操作
#### *3.2.1* 分支创建
- 只创建分支不切换到新分支
```
$ git branch <new-branch-name>
# 这会创建一个指向当前提交对象的指针
```
> 只有当你在该分支提交后，Git 才会把这个指针移动到新的提交对象上。
- 创建新分支并切换到新分支
```
$ git switch -c <new-branch-name>
------------------------------------
$ git checkout -b <new-branch-name>
# 上面的命令等价于
$ git branch <new-branch-name>
$ git checkout <new-branch-name>
```
#### *3.2.2* 分支切换
```
$ git checkout <branch-name>
# 这样 HEAD 就指向了该分支
```
> 分支切换会改变你工作目录中的文件  
> 请牢记：当你切换分支的时候，Git 会重置你的工作目录，使其看起来像回到了你在那个分支上最后一次提交的样子。  
- 强制分支指向指定提交
```
$ git branch -f <branch-name> <commit-id>
# 这会将分支 <branch-name> 强制指向 <commit-id> 所指向的提交对象。
```
#### *3.2.3* 列出分支
```
# 列出所有本地分支
$ git branch
# 查看每一个分支最后一次提交
$ git branch -v
# 列出所有远程分支
$ git branch -r
# 列出所有远程分支的最后一次提交
$ git branch -rv
# 列出所有本地分支和远程分支
$ git branch -a
# 列出所有本地分支和远程分支，以及它们的最后一次提交
$ git branch -av
```

```
# 列出哪些分支已经合并到当前分支
$ git branch --merged
# 列出哪些分支还没有合并到当前分支
$ git branch --no-merged
# 还可以加附加一个参数<branch-name>，列出哪些分支还没有合并到<branch-name>分支或者哪些分支已经合并到<branch-name>分支
$ git branch --merged <branch-name>
$ git branch --no-merged <branch-name>
```
#### *3.2.4* 分支合并
- 合并指定分支到当前分支
```
$ git merge <branch-name>
```
- 合并指定分支到指定分支
```
# 将 <source-branch> 分支合并到 <target-branch> 分支
$ git merge <source-branch> <target-branch>
```
> 若合并过程中没有冲突，Git 会自动将分支指针向前移动， 并将其指向被合并分支所指向的提交对象。  

> 若合并过程中发生了冲突，Git 会停止合并过程，并要求你解决冲突。  
> 为了解决冲突，你必须选择使用由 ======= 分割的两部分中的一个，或者你也可以自行合并这些内容。  
> 解决冲突后，你需要使用 git add 命令将冲突文件添加到暂存区， 然后使用 git commit 命令提交合并。
#### *3.2.5* 分支删除
- 删除本地分支
```
$ git branch -d <branch-name>
```
- 删除远程分支
```
$ git push origin --delete <branch-name>

# 或者
$ git push origin :<branch-name>
# 这种格式在早期的 Git 版本中较为常用
# 它的含义就是告诉 Git，要把一个空分支推送到远程仓库的指定分支位置
# 实际上就相当于删除远程分支。

# 或者
$ git push origin -d <branch-name>
# 此操作仅影响远程仓库，本地仓库中对应的分支仍然存在（如果本地有该分支的话）。
```
### *3.3* 远程分支
> 远程跟踪分支是远程分支状态的引用, 它们以 \<remote>/\<branch> 的形式命名.  

Git 的 clone 命令会为你自动将远程仓库命名为 origin，拉取它的所有数据， 创建一个指向它的 master 分支的指针，并且在本地将其命名为 origin/master。Git还会创建一个与远程跟踪分支指向同一个提交对象的本地master分支,这个本地master分支就是你所在的master分支。
> “origin” 并无特殊含义  
远程仓库名字 “origin” 与分支名字 “master” 一样，在 Git 中并没有任何特别的含义一样。  
> 同时 “master” 是当你运行 git init 时默认的起始分支名字，原因仅仅是它的广泛使用， “origin” 是当你运行 git clone 时默认的远程仓库名字。   
> 如果你运行 git clone -o booyah，那么你默认的远程分支名字将会是 booyah/master。  
#### *3.3.1* 查看远程引用
```
# 列出远程仓库的所有引用信息
# 不仅包括分支（refs/heads/ 下的内容），还涵盖标签（refs/tags/ 下的内容）以及其他可能的引用
$ git ls-remote <remote>
# 专门用于列出远程仓库中所有分支（包括本地跟踪的远程分支）的引用信息
$ git ls-remote --heads <remote>
# 获得远程分支的更多信息
$ git remote show <remote>
```
#### *3.3.2* 跟踪分支
> 跟踪分支是一个指向远程分支的本地分支，它会自动更新以反映远程分支的状态。  
> 在一个跟踪分支上输入 git pull，Git 能自动地识别去哪个服务器上抓取、合并到哪个分支。
##### *3.3.2.1* 创建跟踪分支
```
# 创建一个本地分支，该分支会自动跟踪远程仓库的指定分支
$ git checkout -b <branch> <remote>/<branch>
# 或者
# 这会创建一个本地分支，该分支会自动跟踪远程仓库的指定分支
$ git checkout --track <remote>/<branch>
# 都会自动切换到新的分支

# 更加简洁的方式
$ git checkout <branch>
# 1. Git 会先检查本地是否存在名为 <branch> 的分支
# 2. 如果不存在，Git 接着会在所有远程仓库中查找是否有唯一与之匹配的远程分支.
# 3. 一旦条件满足，Git 会自动创建一个本地的 <branch> 分支，并将其设置为跟踪远程分支的分支,然后立即切换到这个新创建的本地分支
# 4. 如果远程仓库中也不存在名为 <branch> 的分支，Git 会报错
# 这个快捷方式依赖于远程分支名称的唯一性

# 将本地已有分支与远程分支建立跟踪关系
# 在已有分支下执行
# -u 或 --set-upstream-to
$ git branch -u <remote>/<branch> 
```
##### *3.3.2.2* 查看设置的所有跟踪分支
```
$ git branch -vv
```
#### *3.3.3* 删除远程分支
```
$ git push <remote> --delete <branch>
# 基本上这个命令做的只是从服务器上移除这个指针。 Git 服务器通常会保留数据一段时间直到垃圾回收运行，所以如果不小心删除掉了，通常是很容易恢复的。
```
### *3.4* 分支改名
#### *3.4.1* 本地分支改名
```
$ git branch -m <old - branch - name> <new - branch - name>
```
#### *3.4.2* 远程分支改名
```
# 先改本地分支名，再推送新名并删除旧名
# 改本地分支名
$ git branch -m <old - branch - name> <new - branch - name>
# 推送新名分支
$ git push <remote> <new_branch-name>
# 删除旧名分支
$ git push <remote> --delete <old_branch-name>
```
### *3.5* 变基
> 变基操作的目的是将一个分支的提交历史“重新应用”到另一个分支上。   
> 无论是通过变基，还是通过三方合并，整合的最终结果所指向的快照始终是一样的，只不过提交历史不同罢了。  
> 变基过程中，原分支的每个提交都会在基准分支的新上下文下重新创建，提交哈希因父节点变更而更新，故每个提交对应一个新对象。
#### *3.5.1* 变基的基本操作:
[![pVp9CY8.png](https://s21.ax1x.com/2025/05/27/pVp9CY8.png)](https://imgse.com/i/pVp9CY8)  
```
# 分支初始状态如上图
# 现在处于master分支
# 现要将master分支变基到main分支
$ git rebase main
# 1. 执行 git rebase main 时，Git 会把 master 分支基于 main 分支的共同祖先之后的修改提取出来
# 2.然后在 main 分支的最新提交之上重新应用这些修改，从而产生一系列新的提交(可看作产生了一个基于 main 分支最新状态的新提交)
# 3. 并让 master 分支指针指向这个新的提交
# 如下图所示
```
[![pVp9Ayj.png](https://s21.ax1x.com/2025/05/27/pVp9Ayj.png)](https://imgse.com/i/pVp9Ayj)  
```
# 检出到main分支
$ git checkout main
# 进行快进合并master分支
$ git merge master
# 1. 因为 master 分支现在是 main 分支的直接上游（变基后的结果）
# 2. 所以 main 分支可以直接 “快进” 到 master 分支指针所指向的提交位置
# 3. 即将 main 分支指针移动到 master 分支最新提交处，完成合并
# 如下图所示
```
[![pVp9EOs.png](https://s21.ax1x.com/2025/05/27/pVp9EOs.png)](https://imgse.com/i/pVp9EOs)
#### *3.5.2* 截取主题分支上的另一个主题分支，然后变基到其他分支
[![pVpYFOJ.png](https://s21.ax1x.com/2025/05/28/pVpYFOJ.png)](https://imgse.com/i/pVpYFOJ)
```
# 现在有提交记录如上图
# 现在处于master分支
# 并且有一个基于c2提交的主题分支demo_1分支和一个基于c4提交的主题分支demo_2分支

# 现在要截取主题分支demo_1分支的另一个主题分支demo_2分支变基到master分支
$ git rebase master demo_1 demo_2
# 执行后HEAD指针指向产生的新提交c6`
# 效果如下图所示
```
[![pVpYeFx.png](https://s21.ax1x.com/2025/05/28/pVpYeFx.png)](https://imgse.com/i/pVpYeFx)
```
# 切回master分支,进行快速合并
$ git checkout master
$ git merge demo_2
# 效果如下图所示
```
[![pVpYnfK.png](https://s21.ax1x.com/2025/05/28/pVpYnfK.png)](https://imgse.com/i/pVpYnfK)
```
# 将demo_1分支变基到master
$ git rebase master demo_1
# 并切换到master,进行快速合并
$ git checkout master
$ git merge demo_1

# 变基过程中，原分支的每个提交都会在基准分支的新上下文下重新创建，提交哈希因父节点变更而更新，故每个提交对应一个新对象。
# 此处原分支有 3 个提交，因此生成 3 个新提交。
# 效果如下图所示
```
[![pVpYMlD.png](https://s21.ax1x.com/2025/05/28/pVpYMlD.png)](https://imgse.com/i/pVpYMlD)
#### *3.5.3* 变基的注意事项
- 变基操作会改变提交历史，因此在变基之前，一定要确保你已经备份了所有重要的工作。
- 变基操作可能会导致提交历史的混乱，因此在执行变基操作之前，一定要确保你已经备份了所有重要的工作。
> 变基也并非完美无缺，要用它得遵守一条准则：  
> 如果提交存在于你的仓库之外，而别人可能基于这些提交进行开发，那么不要执行变基。
#### *3.5.4* 用变基解决变基
如果遇到有人推送了经过变基的提交，并丢弃了你的本地开发所基于的一些提交的这种情境  
执行 `git rebase <remote>/<branch>` 命令

- 另一种简单的方法 `git pull --rebase` 命令
> 该命令会自动执行 `git fetch` 命令来获取远程分支的最新提交，然后执行 `git rebase` 命令来将本地分支的提交历史重新应用到远程分支的最新提交之上。